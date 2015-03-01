
=== Standard: Create a new EBS volume made to store a redis database ===
# Attach the volume, format it as xfs, map it to /vol, and mount it
ec2-create-volume --availability-zone us-east-1c --size 100
ec2-attach-volume -d /dev/sdh -i INSTANCE_ID VOLUME_ID
grep -q xfs /proc/filesystems || sudo modprobe xfs
sudo mkfs.xfs /dev/sdh

echo "/dev/sdh /vol xfs noatime 0 0" | sudo tee -a /etc/fstab
sudo mkdir -m 000 /vol
sudo mount /vol

# Directions from: http://developer.amazonwebservices.com/connect/entry.jspa?externalID=1663

# Make directories needed for redis data, logs and confs
sudo mkdir -p /etc/redis  /vol/etc/redis
sudo mkdir -p /var/lib/redis /vol/lib/redis
sudo mkdir -p /var/log/redis -p /vol/log/redis

# Mount volume directories at location of original directories
echo "/vol/etc/redis /etc/redis     none bind" | sudo tee -a /etc/fstab
sudo mount /etc/redis

echo "/vol/lib/redis /var/lib/redis     none bind" | sudo tee -a /etc/fstab
sudo mount /var/lib/redis

echo "/vol/log/redis /var/log/redis none bind" | sudo tee -a /etc/fstab
sudo mount /var/log/redis

# Create a user to run the redis process as
sudo adduser --system --disabled-password --disabled-login redis
sudo addgroup redis
sudo adduser redis redis
sudo chown redis:redis /var/log/redis /var/lib/redis

=== Alternate: Prepare a new DB machine to mount a volume already snapshotted with Redis DB data ===
### Create and attach an new volume based on the the snapshot
ec2-create-volume --availability-zone us-east-1c --snapshot SNAPSHOT_ID
ec2-attach-volume -d /dev/sdh -i INSTANCE_ID VOLUME_ID

### Mount volume and make sure the files are owned by redis, just in case UIDs are different on the new instance.
echo "/dev/sdh /vol xfs noatime 0 0" | sudo tee -a /etc/fstab
sudo mkdir -m 000 /vol
sudo mount /vol

sudo find /vol/{lib,log}/redis/ ! -user  root -print0 | sudo xargs -0 -r chown redis
sudo find /vol/{lib,log}/redis/ ! -group root -a ! -group adm -print0 | sudo xargs -0 -r chgrp redis

### Delete original redis dirs
sudo rm -rf /etc/redis
sudo rm -rf /var/lib/redis
sudo rm -rf /var/log/redis

### Recreate dirs in original location
sudo mkdir /etc/redis
sudo mkdir /var/lib/redis
sudo mkdir /var/log/redis

### Set mount points for the volume to the redis directories
echo "/vol/etc/redis /etc/redis     none bind" | sudo tee -a /etc/fstab
echo "/vol/lib/redis /var/lib/redis none bind" | sudo tee -a /etc/fstab
echo "/vol/log/redis /var/log/redis none bind" | sudo tee -a /etc/fstab

### Mount redis directories
sudo mount /etc/redis
sudo mount /var/lib/redis
sudo mount /var/log/redis
