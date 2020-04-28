# Vagrant Commands (have to be in /sites/ycharts)
* vagrant up - build and set up the box
* vagrant reload - shut down the VM and starts the box back up (this will NOT run requirements)
* vagrant provision - runs the provision script (ie. setting up apt-get and requirements)
* vagrant suspend - sleep time for the machine
* vagrant halt - shut down the machine
* vagrant destroy - destroys the machine (CAREFUL: if you do this, vagrant up on the next command will take 30+ minutes!)

## Django Commands
* Migrations
```
vagrant ssh
python manage.py migrate
```

* Run Server
```
# After
vagrant ssh
yc_django
```

# Avoiding Entering Password on  Vagrant Up/Down
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