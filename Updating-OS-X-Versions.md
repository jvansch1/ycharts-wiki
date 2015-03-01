## Re-Configuring Dev Environments for Mac OS X Upgrades


## 10.6 to 10.7

Install XCode command line tools from here (you will need to create an Apple dev account)
http://developer.apple.com/downloads

Reinstall pip

    sudo easy_install pip

Reinstall virtualenvwrapper

    sudo pip install virtualenvwrapper
    source ~/.bash_profile
    workon ycharts

Reinstall requirements

    pip install -r /sites/ycharts/confs/server/requirements_1.txt
    pip install -r /sites/ycharts/confs/server/requirements_2.txt

Reinstall MySQL and add this line to bash_profile

    export DYLD_LIBRARY_PATH=/usr/local/mysql/lib:$DYLD_LIBRARY_PATH

Reinstall MySQL-python (maybe)

    pip uninstall MySQL-python
    pip install MySQL-python
