# Vagrant Commands (have to be in sites/ycharts)
* vagrant up - build and set up the box
* vagrant reload - shut down the VM and starts the box back up (this will NOT run requirements)
* vagrant provision - runs the provision script (ie. setting up apt-get and requirements)
* vagrant suspend - sleep time for the machine
* vagrant halt - shut down the machine
* vagrant destroy - destroys the machine (CAREFUL: if you do this, vagrant up on the next command will take 30+ minutes!)

## Differences
* SQL - MySQL is now run on 0.0.0.0 versus 127.0.0.1. Resetup your MySQL LaunchAgents
```
cp /sites/ycharts/confs/database/com.ycharts.mysql.plist ~/Library/LaunchAgents
launchctl unload ~/Library/LaunchAgents/com.ycharts.mysql.plist
launchctl load ~/Library/LaunchAgents/com.ycharts.mysql.plist
```
* Migrations
```
# Before
python manage.py migrate

# After
vagrant ssh
python manage.py migrate
```
* Run Server
```
# Before
python manage.py runserver
https://127.0.0.1:8000

# After
vagrant ssh
yc_django
Local: 127.0.0.1:8000 (Note the port difference)
```
* Ipython Magic Commands
```
# Before
%paste

# After (This isn't as good, unfortunately)
#cpaste
```

# So you don't have to enter your password on vagrant up/down
```
# run the below, visudo is a file that makes edit to your /etc/sudoers but makes sure it's correct syntax!
sudo visudo
```
paste the below to visudo
```
Cmnd_Alias VAGRANT_EXPORTS_ADD = /usr/bin/tee -a /etc/exports
Cmnd_Alias VAGRANT_NFSD = /sbin/nfsd restart
Cmnd_Alias VAGRANT_EXPORTS_REMOVE = /usr/bin/sed -E -e /*/ d -ibak /etc/exports
%admin ALL=(root) NOPASSWD: VAGRANT_EXPORTS_ADD, VAGRANT_NFSD, VAGRANT_EXPORTS_REMOVE
```