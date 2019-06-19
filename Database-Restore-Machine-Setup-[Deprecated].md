# DEPRECATED
Please see [[Mac Mini (Database Restore Machine) Setup Guide]]

***

### Setting Up OS X

When installing OS X or setting up for the first time

1. Create a user `ycharts` with password `ycharts`

Make sure that **File Sharing** is enabled.

1. To do so, go to *System Preferences* --> *Sharing* and check the box next to **File Sharing**.
1. Add `~/Public` to **Shared Folders** if it is not already there.

Enable **Remote Login**

1. In *System Preferences* --> *Sharing*, check the box next to **Remote Login**.
1. Make a note of the command to login so you can run commands from another computer if necessary.

Make sure the computer won't sleep.

1. In *System Preferences* --> *Energy Saver*, slide **Computer sleep** to **Never**.
1. Check the box for *Start up automatically after a power failure*.
1. In *System Preferences* --> *Desktop & Screen Saver* --> *Screen Saver*, set **Start after:** to **Never**

## Setting up chef-solo

### Install Xcode and Command Line Tools

1. Install Xcode (you will need to create an Apple dev account)
    - http://developer.apple.com/downloads
    - **Version**: 4.6.3

1. Install Xcode Command Line Tools
    - `xcode-select --install`

1. If you have OSX 10.8 or higher, you may also need to install XQuartz manually
    - http://xquartz.macosforge.org/landing/
    - Be sure to log out and log back in after installing!

### Install Homebrew
On the machine run:
```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
### Setup environment
We need our Mac Mini to basically look like our dev setup which means we need the following
 - We need it to have our private key files `ycharts_deploy1` (for github) and `ycharts-2014-01.pem`
 - We need it to have our repo in `/sites/ycharts/`
 - We need it to have a virtualenv `ycharts` (add the `workon ycharts` to the bash profile)
 - We need to install python and pip packages
 - We need to setup the right plists (`com.ycharts.download_and_restore_db.plist` and `com.ycharts.download_and_restore_redis.plist`)


### Install MySQL
We need to install the version we are using in staging/prod. 
At the time of writing this was 5.7.
```bash
# Install MySQL -- DON'T RUN ANY COMMANDS HOMEBREW TELLS YOU TO RUN AFTER INSTALLATION!
brew install mysql

# Make a symlink so generic mysql commands link to 5.7
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

### Install Redis
Make sure you install the same redis version we use in staging/production.
At the time of writing that was 3.2.4
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
```

### Install Special Pip packages
Install redis tools package
```bash
pip install rdbtools==0.1.7
pip install awscli==1.10.38
```


## Test it out

Make sure you can run `mysql` commands

```bash
/usr/local/mysql/bin/mysql --version
```

Make sure you can see something when you run this
```bash
sudo launchctl list | grep ycharts

```

If you get an error, then you need to reinstall MySQL (dumb, I know...)

```bash
brew uninstall mysql55
```

Then re-run the `chef-solo` command again.

### Force the command to run without having to wait to Sunday

```bash
launchctl start com.ycharts.download_and_restore_db
```

See what is happening when you run the above command

```bash
tail -f /sites/ycharts/data/logs/database/download_and_restore_db.out
```

### Stop/Start MySQL

To stop MySQL:

```bash
launchctl unload ~/Library/LaunchAgents/homebrew.mxcl.mysql55.plist
```

To start MySQL:

```bash
launchctl load ~/Library/LaunchAgents/homebrew.mxcl.mysql55.plist
```