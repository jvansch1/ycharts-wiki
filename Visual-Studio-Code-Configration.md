You can download Visual Studio Code [here](https://code.visualstudio.com/Download).

## General / Global Set Up
While we will use VSCode for several different development environments (i.e. Python/Django inside Vagrant for "ycharts" repo and Node inside Docker for "ycharts-chart-generator" report), there are a few things we want to set up globally.

1. Our standard is no lines longer than 120 chars, so help with that, go to _Code->Preferences->Settings_. In the search type ```rulers```. Then you'll see a setting _Editor: Rulers_ come up with a link to _Edit in settings.json_. Click on that and put ```120``` in the list.


## Configure For "ycharts" Repo Development
We need to set it up such that VS Code can work inside Vagrant, that way we get full visibility into our true development environment which means, for example, we can peek into Django classes/methods from right within VS Code.

### Install Remote SSH
1. Command-Shift-P, Type _Extensions: Install Extensions_ and select it.
1. From the search that comes up type _Remote SSH_ and select it to install.

### Get Vagrant ssh-info and copy to ssh config
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

### Connect to Vagrant
1. Command-Shift-P _Remote SSH: Connect to Host_
1. You should see the name of the host we just added come up (in the example above it was _bionic_). 
1. Select it.

A new VSCode window will pop up. Let it work.

### Add Vagrant Folder to Workspace and Save
1. Command-Shift-P _Add Folder to Workspace_
1. Type ```/sites/ycharts```
1. Command-Shift-P _Save Workspace As_
1. DO NOT save in Vagrant, instead click _Show Local_
1. Now in your local machine, save in your home directory as ```ycharts.code-workspace```

You should now be able to close VSCode and go to your home directory and click on _ycharts.code-workspace_. Assuming vagrant is running, that should connect to vagrant and open the _/sites/ycharts_ directory.

NOTE: If you get this error:
```Visual Studio Code is unable to watch for file changes in this large workspace```
Do this https://code.visualstudio.com/docs/setup/linux#_visual-studio-code-is-unable-to-watch-for-file-changes-in-this-large-workspace-error-enospc inside vagrant

### Install Python Extension
The Python extension is a must have for code traversal, linting, etc.

1. Command-Shift-P _Install Extensions_
1. In Search, type _Python_ and install.

#### Select Python Interpreter
1. Command-Shift-P _Python: Select Interpreter_
1. Select for ```/sites/ycharts``` (NOT entire workspace)
1. Choose the one in the virtualenv, it'll be like ```~/.virtualenvs/ycharts/bin/python```

#### Select Python Linter
1. Command-Shift-P _Python: Select Linter_
1. Choose _flake8_. It automatically picks up the _.flake8_ file in ```/sites/ycharts/```!

### Install Other Extensions
1. _Django_ (The first one that comes up, there are two) properly highlights and code completes Django template HTML files.
1. _Document This_ can auto generate docstrings for Javascript classes/methods in the "JSDoc" format.
1. _ESLint_ will lint our JS files.  When you install, you need to then
    1. Go to _Code=>Preferences=>Settings_ menu.
    1. In the search bar, type ```eslint```
    1. Find the setting named _Eslint > Lint Task: Options_
    1. Set it to ```--config /sites/ycharts/confs/developers/.eslintrc.js .```
1. _Python Docstring Generator_ can auto generate docstrings for Python classes/methods. When you install, you need to then: 
    1. Go to _Code=>Preferences=>Settings_ menu.
    1. In the search bar, type ```docstring```
    1. Find the setting named _Auto Docstring: Docstring Format_
    1. Change it ```sphinx```
1. _Angular Language Service_ provides some handy-handy shortcuts when writing Angular code / templates.