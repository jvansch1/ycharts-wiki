You can download Visual Studio Code [here](https://code.visualstudio.com/Download).

NOTE: Much of the below is us doing work to ensure we VS Code can work inside Vagrant, that way we get full visibility into our true development environment which mean, for example, we can peek into Django classes/methods from right within VS Code.

## Install Remote SSH
1. Command-Shift-P, Type "Extensions: Install Extensions" and select it.
2. From the search that comes up type "Remote SSH" and select it to install.

## Get Vagrant ssh-info and copy to ssh config
```
cd /sites/ycharts
vagrant ssh-config
```

It will produce something like this:
```
Host bionic
  HostName 127.0.0.1
  User vagrant
  Port 2222
  UserKnownHostsFile /dev/null
  StrictHostKeyChecking no
  PasswordAuthentication no
  IdentityFile /Users/ara/sites/ycharts/.vagrant/machines/bionic/virtualbox/private_key
  IdentitiesOnly yes
  LogLevel FATAL
  ForwardAgent yes
```

Now
```
open -e ~/.ssh/config
```
and copy the config to the file and save it.




## Create Project for ycharts
1. Launch VS Code
2. File->Add Folder to Workspace /sites/ycharts
3. File->Save Workspace as "ycharts" in your home directory.

## Install Extensions

### Python
1. Install "Python"
2. Click on interpreter on lower right and select the same interpreter we use in our Vagrant box. If you have set up the ycharts_systems repo, you can install any Python you want via entering ```pyenv install X.X.X``` in the terminal. 