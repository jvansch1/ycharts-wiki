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
1. Fork the `ycharts`, `ycharts_chart_generator`, and `ycharts_systems` repos by hitting the "Fork" button. ![Imgur](https://i.imgur.com/m4pVmux.png)
1. Clone the `ycharts`, `ycharts_chart_generator`, and `ycharts_systems` repos

    ```bash
    git clone git@github.com:ycharts/ycharts.git
    git clone git@github.com:ycharts/ycharts_chart_generator.git
    git clone git@github.com:ycharts/ycharts_systems.git
    ```

1. Copy git configs to correct locations so you can push/pull

    ```bash
    cp /sites/ycharts/confs/developers/git_config /sites/ycharts/.git/config
    ```

1. Set up your git pre-commit hooks

    ```bash
    ln -s -f /sites/ycharts/confs/developers/git_pre_commit_hook.py /sites/ycharts/.git/hooks/pre-commit
    ```

## Get SSH keys, AWS keys, etc

1. Get the ycharts keys from someone and move it to `~/.ssh/` so you
can connect to our server machines.  You will be getting the `.pem` key.

2. Also ask your manager to set up an AWS user for you for the project and send you
your access and secret keys. You will need 2 factor authentication on your cell phone so go ahead and download it. ([iOS](https://itunes.apple.com/us/app/google-authenticator/id388497605?mt=8) or [Android](https://play.google.com/store/apps/details?id=com.google.android.apps.authenticator2&hl=en_US))

Initialize your ssh key
```
# Change the permissions on the key file
chmod 700 ~/.ssh/ycharts-2014-01.pem
# Add the key file to your ssh-agent
ssh-add -K ~/.ssh/ycharts-2014-01.pem
```

## Install AutoEnv, set up env specific to this directory
Install Autoenv to manage having different aws access keys as environment variables.
> NOTE: We should probably be moving to use [direnv](https://direnv.net/) soon.

1. brew install autoenv: ```brew install autoenv```
1. After install run: 
```
echo "source $(brew --prefix autoenv)/activate.sh" >> ~/.bash_profile
source ~/.bash_profile
cd /sites/ycharts
touch .env
echo "\nexport AWS_ACCESS_KEY_ID=<YOUR YCHARTS ACCESS KEY>" >> /sites/ycharts/.env
echo "\nexport AWS_SECRET_ACCESS_KEY=<YOUR YCHARTS SECRET KEY>" >> /sites/ycharts/.env
echo "\nexport AWS_DEFAULT_REGION=us-east-1" >> /sites/ycharts/.env
echo "\nssh-add -K ~/.ssh/ycharts-2014-01.pem" >> /sites/ycharts/.env
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
1. Copy the `ycharts_db` folder containing the database files from the database restore machine (Mac Mini)
or another developer's computer to your home folder (`~`). This will take around ~3 hours.
> NOTE: USE THE ETHERNET CABLE. If you have one of the new CableMatters adapters, go to
[their site](http://www.asix.com.tw/download.php?sub=driverdetail&PItemID=131 "Welcome to 1995")
to download the drivers.  Choose: Apple Mac OS X 10.6 to 10.9 Drivers Installer.
2. Once you have a copy of the database folder, you should be able to follow the
[directions for restoring your database](https://github.com/ycharts/ycharts/wiki/Restoring-Database).
and you will have a fresh MySQL 5.7 database! Once the below command finishes, you should have a fresh database.
```
/sites/ycharts/scripts/restore_database.sh -c
```

## Setup ycharts_systems
1. Follow the steps in this Wiki [`ycharts_systems` Developer Environment Setup](https://github.com/ycharts/ycharts_systems/wiki/Development-Environment-Setup#checkout-code).
    - Start from the section [Checkout Code](https://github.com/ycharts/ycharts_systems/wiki/Development-Environment-Setup#checkout-code). Reason is some of the steps in the `ycharts` setup are also present in the `ycharts_systems` setup. There is no need to repeat these steps.


## Create Your Virtual Machine
1. Install [Vagrant 2.1.2](https://releases.hashicorp.com/vagrant/2.1.2/vagrant_2.1.2_x86_64.dmg)
2. Install [Virtualbox 5.2.16](https://download.virtualbox.org/virtualbox/5.2.16/VirtualBox-5.2.16-123759-OSX.dmg)

### Provision your Virtual Machine
1. Run the below commands
```
cd /sites/ycharts
workon ycharts_systems
vagrant --run_initial_setup up --provision
```
> This can take an hour or more.
2. Test that the setup worked correctly.
```
# Test Django
# test django by running a webserver. this will load django
# so that you can access it from your machine at 127.0.0.1:8000

vagrant ssh
yc_django
```

#### Initialize Lists and Sets
You need to run Celery to actually get the lists you need!
```bash
vagrant ssh
yc_celery
```

Now, while Celery is running in a new ssh session
```bash
vagrant ssh
python manage.py security_lists_store_lists_and_sets
```
> NOTE: This will take a few hours. While this is happening, configure your PyCharm editor.

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