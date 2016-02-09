### Homebrew (Mac)
Install Homebrew for easier package management. This will also prompt you about XCode and install it for you.

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

## Checkout code
1. Create a github account and tell an admin to add your username to YCharts.
1. Create an private/public key and tie it to your github account.
    * Follow the instructions here: http://help.github.com/mac-key-setup/
1. Configure so your SSH passphrase is remembered.
    * Follow the instructions here: http://help.github.com/working-with-key-passphrases/.
1. Set up `/sites` directory

    ```bash
    sudo mkdir /sites
    cd /sites

    # Use username to chown /sites
    sudo chown -R `whoami` /sites/
    ```

1. Clone the `ycharts`, `chart_image_generator`, and `developer_setup` repos

    ```bash
    git clone git@github.com:ycharts/ycharts.git
    git clone git@github.com:ycharts/chart_image_generator.git
    git clone git@github.com:ycharts/developer_setup.git
    ```

1. Copy git configs to correct locations so you can push/pull

    ```bash
    cp /sites/ycharts/confs/developers/git_config /sites/ycharts/.git/config
    cp /sites/chart_image_generator/confs/git_config /sites/chart_image_generator/.git/config
    ```

1. Set up your git pre-commit hooks

    ```bash
    ln -s -f /sites/ycharts/confs/developers/git_pre_commit_hook.py /sites/ycharts/.git/hooks/pre-commit
    ln -s -f /sites/chart_image_generator/confs/git_pre_commit_hook.py /sites/chart_image_generator/.git/hooks/pre-commit
    ```

1. Setup your Vagrant Bash Profiles with your Amazon IAM
   Ask someone to create an IAM user for you. Once created, they should give you
   your credentials as well as a temporary password. They will also set up multi-factor
   authentication for you.

   After logging in to [the AWS console](https://ycharts.signin.aws.amazon.com/console), add
   your credentials to your local vagrant bash_profile.
   
   ```
   nano confs/developers/vagrant_bash_profile_local
   ```
1. You can also add aliases you would like to use in vagrant. For example if you want an alias for running your `chart_image_generator` node server you might add a line like this to your `confs/developers/vagrant_bash_profile_local` file.

    ```
    alias yc_node="node /sites/chart_image_generator/ycharts_server.js"
    ```
## Windows Development (Only for emergencies)
```
# ignore chmod differences, otherwise you will see a lot of modified files under PC when you shouldn't - do this 
# in the git repo! 
git config fileMode false
git config --global fileMode false
```
## Connecting to YCharts Production/Staging Servers
Set up your SSH config so you can connect to our server machines.

```bash
cp /sites/ycharts/confs/developers/ssh.conf ~/.ssh/config
```

Get the ycharts key from someone and move it to `~/.ssh/` so you
can connect to our server machines.  You will be getting the `.pem` key.

```bash
# Change the permissions on the key file
chmod 700 ~/.ssh/ycharts-2014-01.pem
# Add the key file to your ssh-agent
ssh-add -K ~/.ssh/ycharts-2014-01.pem
# Test that it works
ssh staging_admin
```

If you were able to connect you will see it, if not ask for help.
(You may need to change `chmod` of the `.pem` key). Now quit.

```bash
logout
```

### Connecting to Other Servers
In order to SSH into other servers that aren't defined in the SSH config, you will need to get the **Internal IP**
of the server you want to connect to, as well as the IP address of the NAT machine for the desired environment,
then run the following command:

```bash
ssh -o ProxyCommand='ssh ec2-user@<nat-ip> -W %h:%p' <internal-ip>
# Currently for staging:
ssh -o ProxyCommand='ssh ec2-user@52.6.96.159 -W %h:%p' <internal-ip>
# Currently for production:
ssh -o ProxyCommand='ssh ec2-user@52.4.31.32 -W %h:%p' <internal-ip>
```

## Install MySQL

```bash
# Install MySQL -- DON'T RUN ANY COMMANDS HOMEBREW TELLS YOU TO RUN AFTER INSTALLATION!
brew install mysql

# Copy YCharts MySQL plist file to correct location so MySQL runs on startup
cp /sites/ycharts/confs/database/com.ycharts.mysql.plist ~/Library/LaunchAgents
# Start MySQL now
launchctl load ~/Library/LaunchAgents/com.ycharts.mysql.plist

# Create ycharts database
mysql -u root

# Run the following commands in the mysql shell
CREATE DATABASE ycharts CHARACTER SET utf8 COLLATE utf8_general_ci;
GRANT ALL ON ycharts.* to 'ycharts'@'localhost' IDENTIFIED BY 'ycharts';
FLUSH PRIVILEGES;

# now get out of mysql
exit
```

The commands to stop and start MySQL are:

```bash
# To stop MySQL
launchctl unload ~/Library/LaunchAgents/com.ycharts.mysql.plist
# To start MySQL
launchctl load ~/Library/LaunchAgents/com.ycharts.mysql.plist
```

### Load MySQL data
Copy the `ycharts_db` folder containing the database files from the database restore machine
or another developer's computer to your home folder (`~`). This can take some time so start
it now and keep going while the files copy over.

Once you have a copy of the database folder, you should be able to follow the
[directions for restoring your database](https://github.com/ycharts/ycharts/wiki/Restoring-Database).
and you will have a fresh MySQL 5.6 database!

> NOTE: You may wish to make sure you are connected to the network over ethernet before attempting
to download a new DB. If you have one of the new CableMatters adapters, go to
[their site](http://www.asix.com.tw/download.php?sub=driverdetail&PItemID=131 "Welcome to 1995")
to download the drivers.  Choose: Apple Mac OS X 10.6 to 10.9 Drivers Installer.

## Create Your Vagrant Instance
```
Install : http://www.vagrantup.com/downloads
# Vagrant 1.8 should auto-download VirtualBox. Talk to Shek if not.
```

## Navigate to your main ycharts directory
```
cd /sites/ycharts
vagrant up
```
This will take about 30 minutes for the first time setup. When it's completed ... test it
```
vagrant ssh

# Test Django
# test django by running a webserver. this will load django
# so that you can access it from your machine at 127.0.0.1:8000
yc_django
```

## Generate JSON Files and Initialize Site Autocompleters
```
python apps/systems/onetime_scripts/init_site_autocompleters_and_generate_json.py
```

## Initialize Lists and Sets
You need to run Celery to actually get the lists you need!
```bash
yc_celery
```

Now, while Celery is running in a new ssh session
```bash
vagrant ssh
python manage.py securities_process_lists_and_sets
```

## Set up press release classifier (Only if you need it)

```bash
python manage.py investor_relations_generate_press_release_classifier
```


## Optimize Vagrant


### Stop the vagrant machine
```
vagrant halt
```

### Load Virtual Box. Click on the virtual machine settings and change the settings below to KVM to make it fast.

[VirtualBox Paravirtualization](http://d.pr/i/1di8R)

## Make Sure Node Works
```
vagrant ssh
yc_node
```
If this doesn't work, delete any precompiled node packages that may be there from a previous setup. remove node_modules from sites/ycharts and sites/chart_image_generator and then make sure you do npm install on both directories INSIDE vagrant. (The way Mac installs node_modules doesn't always work)

## Install FileZilla
https://filezilla-project.org/
Then open up Filezilla and import /sites/ycharts/developers/confs/filezilla_config.xml - this will load common FTP locations we use.
