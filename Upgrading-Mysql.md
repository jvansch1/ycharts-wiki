# Upgrading MySQL 5.5 to 5.6

## Remove Existing MySQL Install

### dmg Installation

If you installed MySQL via the `.dmg` installer (instructions in the dev setup notes), run the
following commands **after** stopping the MySQL server:

```bash
# Delete mysql directory
sudo rm /usr/local/mysql
sudo rm -rf /usr/local/mysql*

# Delete startup item and preferences pane
sudo rm -rf /Library/StartupItems/MySQLCOM
sudo rm -rf /Library/PreferencePanes/My*
rm -rf ~/Library/PreferencePanes/My*

# Delete other mysql files that may exist
sudo rm -rf /Library/Receipts/mysql*
sudo rm -rf /Library/Receipts/MySQL*
sudo rm -rf /private/var/db/receipts/*mysql*
```

Then you need to edit `/etc/hostconfig` and remove the line

```
MYSQLCOM=-YES-
```

Source: http://akrabat.com/computing/uninstalling-mysql-on-mac-os-x-leopard/

### Homebrew Installation

If you install MySQL via Homebrew, run the following commands:

```bash
# Stop the MySQL server!
launchctl unload ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist

# Remove any symlinks you may have (I had the following)
rm ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist
rm /usr/local/mysql

# If you've installed through homebrew/versions
brew uninstall mysql55
# If you installed normally (before 5.6 was released)
brew uninstall mysql
```

## Install MySQL 5.6

At this point, MySQL should be uninstalled (running `which -a mysql` returns `mysql not found`).
We are moving to installing `mysql` via Homebrew as it is easier to keep the same settings across
everyone's computers.

```bash
# Install MySQL -- DON'T RUN ANY COMMANDS HOMEBREW TELLS YOU TO RUN AFTER INSTALLATION!
brew install mysql

# Symlink YCharts MySQL plist file to correct location so MySQL runs on startup
cp /sites/ycharts/confs/database/com.ycharts.mysql.plist ~/Library/LaunchAgents
# Start MySQL now
launchctl load ~/Library/LaunchAgents/com.ycharts.mysql.plist

# Create ycharts database
mysql -u root

# Run the following commands in the mysql shell
CREATE DATABASE ycharts CHARACTER SET utf8 COLLATE utf8_general_ci;
GRANT ALL ON ycharts.* to 'ycharts'@'localhost' IDENTIFIED BY 'ycharts';
FLUSH PRIVILEGES;
```

The commands to stop and start MySQL now are:

```bash
# To stop MySQL
launchctl unload ~/Library/LaunchAgents/com.ycharts.mysql.plist

# To start MySQL
launchctl load ~/Library/LaunchAgents/com.ycharts.mysql.plist
```

At this point, you should be able to follow the
[directions for restoring your database](https://github.com/ycharts/ycharts/blob/develop/docs/developer_notes/mysql/restoring_database.md)
and you will have a fresh MySQL 5.6 database!