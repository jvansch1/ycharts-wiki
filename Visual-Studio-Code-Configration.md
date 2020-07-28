You can download Visual Studio Code [here](https://code.visualstudio.com/Download).

## General / Global Set Up
While we will use VSCode for several different development environments (i.e. Python/Django inside Vagrant for "ycharts" repo and Node inside Docker for "ycharts-chart-generator" report), there are a few things we want to set up globally.

1. Our standard is no lines longer than 120 chars, so help with that, go to ```Code->Preferences->Settings```. In the search type ```rulers```. Then you'll see a setting ```Editor: Rulers``` come up with a link to ```Edit in settings.json```. Click on that and put ```120``` in the list.


## Configure For "ycharts" Repo Development
We need to set it up such that VS Code can work inside Vagrant, that way we get full visibility into our true development environment which means, for example, we can peek into Django classes/methods from right within VS Code.

### Install Remote SSH
1. Command-Shift-P, Type "Extensions: Install Extensions" and select it.
2. From the search that comes up type "Remote SSH" and select it to install.

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
1. Command-Shift-P "Remote SSH: Connect to Host" 
2. You should see the name of the host we just added come up (in the example above it was "bionic"). 
3. Select it.

A new VSCode window will pop up. Let it work.

### Add Vagrant Folder to Workspace and Save
1. Command-Shift-P "Add Folder to Workspace"
2. Type "/sites/ycharts"
3. Command-Shift-P "Save Workspace As"
4. DO NOT save in Vagrant, instead click "Show Local"
5. Now in your local machine, save in your home directory as "ycharts.code-workspace"

You should now be able to close VSCode and go to your home directory and click on "ycharts.code-workspace". Assuming vagrant is running, that should connect to vagrant and open the "/sites/ycharts" directory.

NOTE: If you get this error:
```Visual Studio Code is unable to watch for file changes in this large workspace```
Do this https://code.visualstudio.com/docs/setup/linux#_visual-studio-code-is-unable-to-watch-for-file-changes-in-this-large-workspace-error-enospc inside vagrant

### Install Python Extension
The Python extension is a must have for code traversal, linting, etc.

1. Command-Shift-P "Install Extensions"
2. In Search, type "Python" and install.

#### Select Python Interpreter
1. Command-Shift-P "Python: Select Interpreter" 
2. Select for /sites/ycharts (NOT entire workspace)
3. Choose the one in the virtualenv, it'll be like ```~/.virtualenvs/ycharts/bin/python```

#### Select Python Linter
1. Command-Shift-P "Python: Select Linter" 
2. Choose "flake8"

### Install Other Extensions
1. _Django_ (The first one that comes up, there are two) properly highlights and code completes Django template HTML files.
2. _Document This_ can auto generate docstrings for Javascript classes/methods in the "JSDoc" format.
3. _ESLint_ will lint our JS files.
4. _Python Docstring Generator_ can auto generate docstrings for Python classes/methods. IMPORTANT: When you install this, go to the settings and select the "sphinx" format, which is what we use!
