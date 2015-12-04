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

1. Open Xcode and go to *Preferences* --> *Downloads* and install **Command Line Tools**

1. If you have OSX 10.8 or higher, you may also need to install XQuartz manually
    - http://xquartz.macosforge.org/landing/
    - Be sure to log out and log back in after installing!

### Install Chef

```bash
curl -L https://www.opscode.com/chef/install.sh | sudo bash
```

### Run Chef

If you are setting up the core machine which will execute the dump (New York) run

```bash
sudo chef-solo \
    -j "https://s3.amazonaws.com/ycharts-utils/chef/new_york.json" \
    -r "https://s3.amazonaws.com/ycharts-utils/chef/ycharts_db.tar.gz" \
    -l debug
```

If you are setting up a machine that will just download an existing dump (anywhere else) run

```bash
sudo chef-solo \
    -j "https://s3.amazonaws.com/ycharts-utils/chef/chicago.json" \
    -r "https://s3.amazonaws.com/ycharts-utils/chef/ycharts_db.tar.gz" \
    -l debug
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