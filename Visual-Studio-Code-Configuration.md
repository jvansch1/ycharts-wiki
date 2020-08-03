You can download Visual Studio Code [here](https://code.visualstudio.com/Download).

## General / Global Set Up
While we will use VSCode for several different development environments (i.e. Python/Django inside Vagrant for "ycharts" repo and Node inside Docker for "ycharts-chart-generator" report), there are a few things we want to set up globally.

Do do this, Command-Shift-P, Type "Open Settings (JSON)"
You will see empty JSON file you can edit with settings, put this in it:
```
{
    "editor.rulers": [120],
    "editor.minimap.enabled": false,
    "workbench.editor.enablePreviewFromQuickOpen": false
}
```

This does a few things
1. Create a line at 120 characters so you know when you go over, since that is our standard
1. Turn of the right side minimap of files which is slow and annoying.
1. Turn of the feature where from opening a file from search it opens in preview mode, which is confusing and annoying.

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
NOTE: Installing extensions while in the ycharts workspace makes it such that we are actually installing these extensions inside the vagrant box. If you'd like to use these extensions outside vagrant you'd have to close this workspace and install them again "Locally".

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
1. _XML Tools_ formats giant XML files, which we often get from data providers.

## Configure For "ycharts-chart-generator" Repo Development

### Add Folder to Workspace and Save
1. Command-Shift-P _Add Folder to Workspace_
1. Type ```/sites/ycharts-chart-generator```
1. Command-Shift-P _Save Workspace As_
1. Save in your home directory as ```ycharts-chart-generator.code-workspace```

### Install Extensions
1. Docker
2. eslint
