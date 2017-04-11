### Homebrew (Mac)
Install Homebrew for easier package management. This will also prompt you about XCode and install it for you.

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

## Setting up Git
1. Create a github account and tell an admin to add your username to YCharts.
1. Create an private/public key and tie it to your github account.
    * Follow the instructions here: https://help.github.com/articles/connecting-to-github-with-ssh/
1. Configure so your SSH passphrase is remembered.
    * Follow the instructions here: https://help.github.com/articles/working-with-ssh-key-passphrases/.
1. Add your ssh key to ssh-agent explicitly just in case though

    ```bash
    ssh-add -K ~/.ssh/id_rsa
    ```
1. Set up `/sites` directory

    ```bash
    sudo mkdir /sites
    cd /sites

    # Use username to chown /sites
    sudo chown -R `whoami` /sites/
    ```

## Checkout Code
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

## Get SSH keys, AWS keys, etc

Get the ycharts key from someone and move it to `~/.ssh/` so you
can connect to our server machines.  You will be getting the `.pem` key.

Also ask someone to set up an AWS user for you for the project and send you
your access and secret keys.

Initialize your ssh key
```
# Change the permissions on the key file
chmod 700 ~/.ssh/ycharts-2014-01.pem
# Add the key file to your ssh-agent
ssh-add -K ~/.ssh/ycharts-2014-01.pem
# Make sure all ssh keys are added to the ssh-agent on login
printf "\nssh-add -A\n" >> ~/.bash_profile
source ~/.bash_profile
```

## Install AutoEnv, set up env specific to this directory
Install Autoenv to manage having different aws access keys.
Be aware of the caveats that follow the install process
Consult documentation for install https://github.com/kennethreitz/autoenv, however..
```
brew install autoenv
```
After Install:
```
echo "source $(brew --prefix autoenv)/activate.sh" >> ~/.bash_profile
source ~/.bash_profile
```

Create an ".env" file in root of the project and add:

```
export AWS_ACCESS_KEY_ID=<YOUR YCHARTS ACCESS KEY>
export AWS_SECRET_ACCESS_KEY=<YOUR YCHARTS SECRET KEY>
export AWS_DEFAULT_REGION=us-east-1

cp /sites/ycharts/confs/developers/ssh.conf ~/.ssh/config
ssh-add -A
```

Now exit and enter the directory and make sure it's working!
```bash
cd .
```

## Install Redis
```bash
# Download redis 3.2.4
cd ~
curl http://download.redis.io/releases/redis-3.2.4.tar.gz -o "redis-3.2.4.tar.gz"
tar xzf redis-3.2.4.tar.gz
cd redis-3.2.4
make

# Install redis executables and redis config
mv ~/redis-3.2.4/src/redis-server /usr/local/bin
mv ~/redis-3.2.4/src/redis-cli /usr/local/bin
cp /sites/ycharts/confs/redis/redis.conf /usr/local/etc/redis.conf
# Start redis on launch
cp /sites/ycharts/confs/redis/com.ycharts.redis.plist ~/Library/LaunchAgents
# Start redis now
launchctl load ~/Library/LaunchAgents/com.ycharts.redis.plist

# Use the redis cli with:
redis-cli
```

NOTE: If after the above, redis-server is not running, check the permissions of
```
/usr/local/var/log/
```
It should YOURUSERNAME:admin!

## Install MySQL

```bash
# Install MySQL -- DON'T RUN ANY COMMANDS HOMEBREW TELLS YOU TO RUN AFTER INSTALLATION!
brew install mysql

# Make a symlink so generic mysql commands link to 5.7 version
ln -s /usr/local/opt/mysql /usr/local/opt/mysql

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
Install Vagrant: https://www.vagrantup.com/downloads.html
Install VirtualBox: https://www.virtualbox.org/wiki/Downloads
```

## Navigate to your main ycharts directory
```
cd /sites/ycharts
vagrant up
```
This can take an hour or more. HOWEVER, there is a shortcut if you are in the NYC office.

From the Mac Mini Shared Folder, copy vagrant_dev_setup_packaged.box to /sites/ycharts/confs/developers/vagrant_dev_setup_packaged.box
```
cd /sites/ycharts
vagrant box add vagrant_dev_setup_packaged /sites/ycharts/confs/developers/vagrant_dev_setup_packaged.box
vagrant up
```
This will take about 5 minutes for the first time setup. When it's completed ... test it

```
# Test Django
# test django by running a webserver. this will load django
# so that you can access it from your machine at 127.0.0.1:8000

vagrant ssh
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

## Make Sure Node Works
```
vagrant ssh
yc_node
```
If this doesn't work, delete any precompiled node packages that may be there from a previous setup. remove node_modules from sites/ycharts and sites/chart_image_generator and then make sure you do npm install on both directories INSIDE vagrant. (The way Mac installs node_modules doesn't always work)
```
npm cache clean
# Remove all installed packages and other NPM stuffs
rm -rf ~/.npm
rm -rf /sites/chart_image_generator/node_modules
rm -rf /sites/ycharts/node_modules
cd /sites/ycharts
npm install
cd /sites/chart_image_generator
npm install
yc_node
```

## Amazon IAM Roles
1. Setup your Vagrant Bash Profiles with your Amazon IAM
   Ask someone to create an IAM user for you. Once created, they should give you
   your credentials as well as a temporary password. They will also set up multi-factor
   authentication for you.

   After logging in to [the AWS console](https://ycharts.signin.aws.amazon.com/console), add
   your credentials to your local vagrant bash_profile.
   
   ```
   # if you have sublime
   subl /sites/ycharts/confs/developers/vagrant_bash_profile_local
   # otherwise open up with nano
   nano /sites/ycharts/confs/developers/vagrant_bash_profile_local

   # then in your vagrant_bash_profile_local add
   export AWS_ACCESS_KEY_ID=<YOUR_ACCESS_KEY_HERE>
   export AWS_SECRET_ACCESS_KEY=<YOUR_SECRET_ACCESS_KEY_HERE>
   export AWS_DEFAULT_REGION=us-east-1
   # add any other aliases you want and close it.

   # you need to now 'source' your vagrant_bash_profile_local so run:
   vagrant halt
   vagrant up --provision
   ```
1. You can also add aliases you would like to use in vagrant. For example if you want an alias for running your `chart_image_generator` node server you might add a line like this to your `confs/developers/vagrant_bash_profile_local` file.

    ```
    alias yc_node="node /sites/chart_image_generator/ycharts_server.js"
    ```
