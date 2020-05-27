### Setup VPN
Make sure you can access the YCharts VPN and have an account. 


### Enable FileVault
For security purposes, all of our machines need to have encrypted hard disks. Before you do anything on your computer you will need to install FileVault. Please read the following steps:

1. Note before following the guide remember to:
    - NOT link it to your iCloud.
    - Write down your secret key.
1. Please follow the instructions on how to secure your hard drive with FileVault [here](https://support.apple.com/en-us/HT204837#turnon).
1. Once it is completed (this can take a few hours) please notify Fox or Ara so they can verify and mark it internally.

## Install Homebrew
Install Homebrew for easier package management. This will also prompt you about XCode and install it for you.

```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
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
    sudo mkdir -p /sites
    cd /sites

    # Use username to chown /sites
    sudo chown -R `whoami` /sites/
    ```

## Setting up Git-Secret
1. Install `git-secret` via Homebrew
    ```bash
    brew install git-secret
    ```
1. Install `gpg`. 
    ```bash
    brew install gnupg
    ```
1. Create a `gpg` key. Follow the instructions [here](https://help.github.com/en/articles/generating-a-new-gpg-key#generating-a-gpg-key). You should add this `GPG` key to your github account as well.
1. Export the `GPG` by following the instructions below. Send whoever is guiding your onboarding experience your public key in a `.txt` file. Do not proceed until they have pushed your keyring. Ask them to confirm before moving forward with the setup.
    ```bash
    # Export Public key via
    # Find KEY ID
    gpg --list-secret-keys --keyid-format LONG
    # Print Public Key to terminal.
    gpg --armor --export [KEY ID]
    ```
1. The onboarder needs to do the following before you can proceed:
    ```bash
    # Download the public key to a local directory (ie ~/Downloads/new_engineer.txt)
    gpg --import ~/Downloads/new_engineer.txt
    cd /sites/ycharts
    git checkout develop
    git pull origin develop
    git secret tell newengineer@example.com
    git secret reveal -f; git secret hide
    git commit -am "Added Engineer's Name to git secret."
    git push origin develop
    ```
## Checkout Code
1. Fork the [`ycharts`](https://github.com/ycharts/ycharts), [`ycharts_chart_generator`](https://github.com/ycharts/ycharts_chart_generator), and [`ycharts_systems`](https://github.com/ycharts/ycharts_systems) repos by hitting the "Fork" button. ![Imgur](https://i.imgur.com/N5Y9E6a.png)
1. Clone the `ycharts` and `ycharts_chart_generator` repos

    ```bash
    git clone git@github.com:ycharts/ycharts.git
    git clone git@github.com:ycharts/ycharts_chart_generator.git
    ```

1. Confirm that you can pull down from one of our remote branches; if you can't ask for assistance!

    ```bash
    cp /sites/ycharts/confs/developers/git_config /sites/ycharts/.git/config
    cd /sites/ycharts
    git fetch --dry-run origin
    ```

1. Set up your git hooks for the `ycharts` repo

    ```bash
    ln -s -f /sites/ycharts/confs/developers/git_pre_commit_hook.py /sites/ycharts/.git/hooks/pre-commit
    ln -s -f /sites/ycharts/confs/developers/git_post_merge_hook.py /sites/ycharts/.git/hooks/post-merge
    ```

1. Decrypt sensitive files in `ycharts` repo

    ```bash
    cd /sites/ycharts
    git secret reveal -f
    ```
## Get SSH keys, AWS keys, etc

1. Get the ycharts keys from someone and move it to `~/.ssh/` so you
can connect to our server machines.  You will be getting the `.pem` key.

2. Also ask your manager to set up an AWS user for you for the project and send you
your access and secret keys. You will need 2-factor authentication on your cell phone so go ahead and download it. ([iOS](https://itunes.apple.com/us/app/google-authenticator/id388497605?mt=8) or [Android](https://play.google.com/store/apps/details?id=com.google.android.apps.authenticator2&hl=en_US))

Initialize your ssh key
```
# Change the permissions on the key file
chmod 700 ~/.ssh/ycharts-2014-01.pem
# Add the key file to your ssh-agent
ssh-add -K ~/.ssh/ycharts-2014-01.pem
```

## Setup ycharts_systems
1. Follow the steps in this Wiki [`ycharts_systems` Developer Environment Setup](https://github.com/ycharts/ycharts_systems/wiki/Development-Environment-Setup).

## Set up Autoenv specific to this directory
Autoenv should have already been installed when going through the environment setup for ycharts-systems repo.

> NOTE: We should probably be moving to use [direnv](https://direnv.net/) soon.

```sh
cd /sites/ycharts

touch .env
echo "workon ycharts_systems" >> /sites/ycharts/.env

touch /sites/ycharts/confs/developers/vagrant_bash_profile_local
echo "export AWS_ACCESS_KEY_ID=<YOUR YCHARTS ACCESS KEY>" >> /sites/ycharts/confs/developers/vagrant_bash_profile_local
echo "export AWS_SECRET_ACCESS_KEY=<YOUR YCHARTS SECRET KEY>" >> /sites/ycharts/confs/developers/vagrant_bash_profile_local
echo "export AWS_DEFAULT_REGION=us-east-1" >> /sites/ycharts/confs/developers/vagrant_bash_profile_local
```

Now re-enter the directory and make sure it's working. You should be prompted and then type y.

```sh
cd .
``` 

## Install NVM
```
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh | bash

echo "export NVM_DIR=\"$HOME/.nvm\"" >> ~/.bash_profile
echo "[ -s \"$NVM_DIR/nvm.sh\" ] && \. \"$NVM_DIR/nvm.sh\" # This loads nvm"  >> ~/.bash_profile
echo "[ -s \"$NVM_DIR/bash_completion\" ] && \. \"$NVM_DIR/bash_completion\" # This loads nvm bash_completion"  >> ~/.bash_profile
source ~/.bash_profile
nvm install 8
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
mkdir -p ~/Library/LaunchAgents
cp /sites/ycharts/confs/redis/com.ycharts.redis.plist ~/Library/LaunchAgents
# Start redis now
launchctl load ~/Library/LaunchAgents/com.ycharts.redis.plist

# Use the redis cli with:
redis-cli
```

## Install MySQL

```bash
# Install MySQL -- DON'T RUN ANY COMMANDS HOMEBREW TELLS YOU TO RUN AFTER INSTALLATION!
brew install mysql@5.7

echo 'export PATH="/usr/local/opt/mysql@5.7/bin:$PATH"' >> ~/.bash_profile
source ~/.bash_profile

# Make a symlink so generic mysql commands link to 5.7 version
ln -s /usr/local/opt/mysql@5.7 /usr/local/opt/mysql

# Copy YCharts MySQL plist file to correct location so MySQL runs on startup
mkdir -p ~/Library/LaunchAgents
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
Now that MySQL is installed you need data in there. Every week we dump our most recent staging data to a server that is on the premise so that if developers need a new database they can get it whenever they want. You will need to do this right now while you set your environment up but this will also be something you do once a week or once every couple of weeks so that you always have data locally that reflects what is in staging. 

1. Before you start copying 400GB of data. Make sure you are connected to the internet via an Ethernet. You should have a Ethernet to USB-C adapter with you. If you don't please ask Fox for one since someone forget to put it on your desk. If you have one of the new CableMatters adapters, go to
[their site](http://www.asix.com.tw/download.php?sub=driverdetail&PItemID=131 "Welcome to 1995")
to download the drivers.  Choose: Apple Mac OS X 10.6 to 10.9 Drivers Installer.
1. Open the finder and you should see the YCharts's Mac Mini in your "Shared" network. It will prompt you to login. Ask the nearest developer or Fox what the password and username is.
1. Copy the `ycharts_db` folder containing the database files from the database restore machine (Mac Mini)
or another developer's computer to your home folder (`~`). This will take around ~3 hours.
2. Once you have a copy of the database folder, you should be able to follow the
[directions for restoring your database](https://github.com/ycharts/ycharts/wiki/Restoring-Database).
and you will have a fresh MySQL 5.7 database! Once the below command finishes, you should have a fresh database.
```bash
/sites/ycharts/scripts/restore_database.sh -c
```

## Create Your Virtual Machine
Now you will need to create your local Linux VM machine that will serve as your local development environment.
1. Install [Vagrant 2.1.2](https://releases.hashicorp.com/vagrant/2.1.2/vagrant_2.1.2_x86_64.dmg)
2. Install [Virtualbox 5.2.16](https://download.virtualbox.org/virtualbox/5.2.16/VirtualBox-5.2.16-123759-OSX.dmg)

### Configure NFS
More detailed instructions of configuring your NFS can be found in Vagrant's [documentation](https://www.vagrantup.com/docs/synced-folders/nfs.html#root-privilege-requirement), however, the following steps is all you should need to do.

1. Edit the sudoers file (must be done as root using the visudo command)
```bash
sudo visudo
```
2. Add `Cmnd_Alias` entries. Under the line `# Cmnd alias specification`
```
# Cmnd alias specification
Cmnd_Alias VAGRANT_EXPORTS_ADD = /usr/bin/tee -a /etc/exports
Cmnd_Alias VAGRANT_NFSD = /sbin/nfsd restart
Cmnd_Alias VAGRANT_EXPORTS_REMOVE = /usr/bin/sed -E -e /*/ d -ibak /etc/exports
```
3. Add User priveleges. The `# User privilege specification` should look like.
```
# User privilege specification
root    ALL=(ALL) ALL
%admin  ALL=(ALL) ALL
%admin ALL=(root) NOPASSWD: VAGRANT_EXPORTS_ADD, VAGRANT_NFSD, VAGRANT_EXPORTS_REMOVE
```
4. Save and close the `/etc/sudoers` file. You are good to go.

### Provision your Virtual Machine
1. Run the below commands
```bash
cd /sites/ycharts
workon ycharts_systems
vagrant --run_initial_setup up --provision
```
> This can take an hour or more.
2. Test that the setup worked correctly.
```bash
# Test Django
# test django by running a webserver. this will load django
# so that you can access it from your machine at 127.0.0.1:8000

vagrant ssh
yc_migrate
yc_django
```

#### Initialize Lists and Sets
What are list and sets? Good question! So when we talk about "lists" and "sets" we are usually referring to the list and sets we store in redis. They power parts of our application and to have a fully setup local environment you will need to populate the redis you installed in the previous steps.

1. In order to populate redis we need Celery to be running.
```bash
# Start Celery
vagrant ssh
yc_celery
# do not close this window. Let it sit.
```
2. Open a new window on your Terminal and SSH into your vagrant box. Run each of the `python` commands in a new `screen` session (e.g. `screen -d -m python /sites...`
> NOTE: This will take a few hours. While this is happening, configure your PyCharm editor.
```bash
vagrant ssh
python /sites/ycharts/manage.py calculations_store_sets
python /sites/ycharts/manage.py securities_info_store_sets
python /sites/ycharts/manage.py security_lists_store_lists
```

## Configure PyCharm

Follow the steps here to configure PyCharm: https://github.com/ycharts/ycharts/wiki/PyCharm-Configuration

## Local User account

### Create User
You will also need to create a new `User` account in your local database, with access to all of YChart's features. To get around the catch-22 of setting up a new user via the `/admin` panel, enter the django shell:

```bash
vagrant ssh
python manage.py shell_plus
```
and create a new user:
```python
user = User(email=<your_email>, is_staff=True, is_superuser=True)
user.set_password(<your_password>)
user.save()
```

### Setup User
1. Now, start a django server by ssh into Vagrant (`cd /sites/ycharts && vagrant ssh`)
1. Now once you are inside your vagrant instance run `yc_django` and go to `http://0.0.0.0:8000/admin/` to log in. 
1. From here, head to `Users` section and create a PartnerSubscription for your account. ![](https://imgur.com/RuJsro3.png)  ![](https://imgur.com/UkMLmTl.png)
1. Add your user to the [YCharts Staff Client Group](http://localhost:8000/admin/accounts/clientgroup/62/) [![Screenshot from Gyazo](https://gyazo.com/faf60276b187e0622509315ec3160696/raw)](https://gyazo.com/faf60276b187e0622509315ec3160696)

> Note: You should now have access to all features in your local environment however you will need to do the same in staging and production. 

## Running Webpack Locally
Our newer redesigned frontend uses webpack to compile and bundle our frontend static assets. When running the site locally webpack has the ability to watch for any file changes and re-compile any JS or CSS if the files change. This process is known as "hot reloading". This feature makes it very easy to write JS and reload your local page and see the changes you made take effect immediately.

#### Webpack Hot Reloading
To get webpack to hot reload your assets you need to run:
```bash
cd /sites/ycharts
npm run dev
```

If you run `npm run dev` in your Vagrant Virtual Machine you will notice that it takes a long time to pick up any file changes because the virtual machine is accessing your Mac's filesystem via NFS. So the way to get hot-reloading to work well is to run it on your Mac host machine. To do this you need to do the following:
```bash
cd /sites/ycharts
# Remove the node modules directory since it was installed on your VM with Linux and thus some packages are not compatible with your mac.
rm -rf node_modules/ 
# Tells your mac to use Node version 10
nvm use 10
# Re-install node modules on your mac so they are compatible with the host machine. 
npm i
# Run webpack hot reloading
npm run dev
```

**IMPORTANT NOTE:**
When running webpack hot reloading on your local host machine (Mac), do not check in the `package-lock.json` if it is modified. You should only checkin the `package-lock.json` if you have modified anything in `package.json` and have run `npm run dev` within your vagrant machine. 