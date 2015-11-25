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
* Running requirements
```
# Before
pip install -r confs/server/dev_requirements.txt
pip install -r confs/server/requirements.txt

# After
vagrant provision
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
djangoserver
OSX: 127.0.0.1:4000 (Note the port difference)
```

