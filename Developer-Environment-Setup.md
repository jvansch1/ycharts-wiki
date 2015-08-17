## Install Some Devs Tool We Need
### XCode
Install XCode (full install) AND command line tools from here
(you will need to create an Apple dev account)
http://developer.apple.com/downloads

### XQuartz
If you have OSX 10.8 or higher, you may also need to install XQuartz manually:
http://xquartz.macosforge.org/landing/

### Homebrew
Install Homebrew for easier package management

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

## Install Some Random Dev Packages We Need

```bash
# For the pre commit hook
brew install ack
# For Numpy/SciPy/Pandas
brew install gcc
# For pylibmc
brew install libmemcached
# For weasyprint
brew install pango gdk-pixbuf libxml2 libxslt libffi
# If you're using OSX 10.9+:
brew install swig
# Install pkg-config (may be necessary for some other packages)
brew install pkg-config
```

## Checkout code
1. Create a github account and tell an admin your username. They must add you to YCharts
1. Create an private/public key and tie it to your github account.
    * Follow the instructions here: http://help.github.com/mac-key-setup/
1. Configure so your SSH passphrase is remembered.
    * Follow the instructions here: http://help.github.com/working-with-key-passphrases/.
    * For Mac, you won't have to do anything like set up ssh-agent as long as you're using the default key name
1. Fork the code on github for `ycharts` and `ycharts_node` to your own repo.
1. Set up `/sites` directory

    ```bash
    sudo mkdir /sites
    cd /sites
    # Get your username
    whoami
    # Use username to chown /sites
    sudo chown -R <username> /sites/
    ```

1. Clone the `ycharts` and `ycharts_node` repos

    ```bash
    git clone git@github.com:ycharts/ycharts.git ycharts
    git clone git@github.com:ycharts/ycharts_node.git ycharts_node
    ```

1. Copy git configs to correct locations so you can push/pull

    ```bash
    cp /sites/ycharts/confs/developers/git_config /sites/ycharts/.git/config
    cp /sites/ycharts_node/confs/git_config /sites/ycharts_node/.git/config
    ```

1. Set up your git pre-commit hooks

    ```bash
    ln -s -f /sites/ycharts/confs/developers/git_pre_commit_hook.py /sites/ycharts/.git/hooks/pre-commit
    ln -s -f /sites/ycharts_node/confs/git_pre_commit_hook.py /sites/ycharts_node/.git/hooks/pre-commit
    ```

1. Set up Pivotal Tracker integration
    1. On Pivotal Tracker go to "Profile" and scroll down to the bottom where it says "API Token".
        * If there is not already an API Token listed, click "Create New Token" and then copy it.
    1. On GitHub, browse to your own fork of the `ycharts` or `ycharts_node` repo (i.e. `your_name/ycharts`).
    1. Go to "Settings" --> "Webhooks & Services" --> "Add service" --> "Pivotal Tracker".
    1. Paste the API token from above in the "Token" box, check the "Active" checkbox, and click "Update Settings".

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

## Install Node

```bash
brew update
brew tap homebrew/versions
brew install node010
```

Pay special attention to the post install directions.
It may tell you add to your $PATH in your `~/.bash_profile`

## Install Node Packages

```bash
cd sites/ycharts
npm install
```

## Install Cairo
```bash
brew install jpeg
brew install cairo
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

## Install Memcached

```bash
brew install memcached

# Symlink plist to launch memcached on startup
ln -sfv /usr/local/opt/memcached/*.plist ~/Library/LaunchAgents
# Start memcached now
launchctl load ~/Library/LaunchAgents/homebrew.mxcl.memcached.plist
```

## Install Redis

```bash
brew install redis28

# Symlink plist to launch redis on startup
ln -sfv /usr/local/opt/redis28/*.plist ~/Library/LaunchAgents
# Start redis now
launchctl load ~/Library/LaunchAgents/homebrew.mxcl.redis28.plist

# Use the redis cli with:
redis28-cli
```

## Install Mercurial

Download and install the latest Mercurial binary for OSX, http://mercurial.selenic.com/,
or to install via Homebrew

```bash
brew install mercurial

# Tell python where to find the installed site-packages
mkdir -p ~/Library/Python/2.7/lib/python/site-packages
echo '/usr/local/lib/python2.7/site-packages' > ~/Library/Python/2.7/lib/python/site-packages/homebrew.pth
```

## Install Pip

```bash
sudo easy_install pip
```

## Install Virtual Environment

Basic Install

```bash
sudo pip install virtualenv
sudo pip install virtualenvwrapper
mkdir ~/.virtualenvs
```

Open `~/.bash_profile` file and add the following lines

```bash
export WORKON_HOME=$HOME/.virtualenvs
source /usr/local/bin/virtualenvwrapper.sh
```

Now run the file so it takes effect

```bash
source ~/.bash_profile
```

## Creating the Virtual Env

```bash
# Make the new virtualenv
mkvirtualenv --distribute ycharts

# Go inside ycharts virtualenv
workon ycharts

# Exit ycharts virtualenv
deactivate
```

> NOTE: For the next few steps, you will want them installed in the
> virtualenv you have setup!

## Setup iPython
```bash
# Install iPython
pip install ipython
# Install ipdb
pip install ipdb
```

To set a debug trace, add the following line where you want a breakpoint
in your code.

```python
import ipdb; ipdb.set_trace()
```

> NOTE: If you get a warning about `readline` when inside an ipython shell:

```bash
easy_install readline
```

## Installing required Python packages for YCharts

```bash
pip install -r /sites/ycharts/confs/server/requirements.txt
pip install -r /sites/ycharts/confs/server/dev_requirements.txt
```

### Install additional files for textblob
Add the following line to your `~/.bash_profile`
```bash
export NLTK_DATA=/sites/ycharts/data/investor_relations/nltk_data
```

Then run
```bash
source ~/.bash_profile
python -m textblob.download_corpora
```

### Potential Issues

#### XCode 5

If you have Xcode 5 or greater and you get errors that look like

```
clang: error: unknown argument: '-mno-fused-madd' [-Wunused-command-line-argument-hard-error-in-future]
clang: note: this will be a hard error (cannot be downgraded to a warning) in the future
error: command 'clang' failed with exit status 1
```

when installing compiled packages, (numpy, Pillow, etc.) then those warnings are indeed hard errors,
and you need to set the `ARCHFLAGS` environment variable:

```bash
# Add to ~/.bash_profile to make it permanent
export ARCHFLAGS=-Wno-error=unused-command-line-argument-hard-error-in-future
```

Source: http://stackoverflow.com/questions/22394575/xcode-llvm-5-1-clang-error

#### Other Issues

If you still have issues, you may need to specify the following flags:

For OS X 10.8 or less, you might need to do this:

```bash
export CC="gcc" CXX="g++" FFLAGS="-ff2c"
export ARCHFLAGS="-arch i386 -arch x86_64 -Wno-error=unused-command-line-argument-hard-error-in-future"
```

> NOTE: For OSX 10.9+ you may need to install gcc49, and then slightly change the above.

```bash
# Tap homebrew/versions and install gcc49
brew tap homebrew/versions
brew install gcc49

# Set correct flags
export CC="gcc-4.9" CXX="g++-4.9" FFLAGS="-ff2c"
export ARCHFLAGS="-arch i386 -arch x86_64 -Wno-error=unused-command-line-argument-hard-error-in-future"
```

## Set Up Local Dev Settings

```bash
echo "from development import *" > /sites/ycharts/ycharts/settings/active.py
```

## Generate JSON Files

```bash
python manage.py calculations_score_autocompleter
python manage.py calculations_store_menus
python manage.py companies_store_menus
python manage.py fund_attributes_store_menus
python manage.py securities_store_menus
```

## Initialize Site Autocompleters

> NOTE: You need some of the processes installed above to be running. It is recommended that you
> reboot your computer at this time as they should be set to run on startup.
>
> NOTE: If you got an older database, you may need to run migrations: `python manage.py migrate`

```bash
# Initialize security autocompleters
python manage.py autocompleter_init --remove --store --name company
python manage.py autocompleter_init --remove --store --name etf
python manage.py autocompleter_init --remove --store --name cef
python manage.py autocompleter_init --remove --store --name stock
python manage.py autocompleter_init --remove --store --name fund
python manage.py autocompleter_init --remove --store --name main

# Initialize calc autocompleters
python manage.py autocompleter_init --remove --store --name all_calc
python manage.py autocompleter_init --remove --store --name aggregate_calc
python manage.py autocompleter_init --remove --store --name company_calc
python manage.py autocompleter_init --remove --store --name company_history_calc
python manage.py autocompleter_init --remove --store --name etf_calc
python manage.py autocompleter_init --remove --store --name etf_history_calc
python manage.py autocompleter_init --remove --store --name fund_calc
python manage.py autocompleter_init --remove --store --name fund_history_calc
python manage.py autocompleter_init --remove --store --name history_calc
python manage.py autocompleter_init --remove --store --name index_calc
python manage.py autocompleter_init --remove --store --name index_history_calc
python manage.py autocompleter_init --remove --store --name mutual_fund_calc
python manage.py autocompleter_init --remove --store --name mutual_fund_history_calc
python manage.py autocompleter_init --remove --store --name stock_calc
python manage.py autocompleter_init --remove --store --name stock_history_calc
python manage.py autocompleter_init --remove --store --name technical_calc

# Initialize other autocompleters
python manage.py autocompleter_init --remove --store --name fund_benchmark
python manage.py autocompleter_init --remove --store --name fund_broker
python manage.py autocompleter_init --remove --store --name fund_family
python manage.py autocompleter_init --remove --store --name fund_manager

python manage.py autocompleter_init --remove --store --name all_info_field
python manage.py autocompleter_init --remove --store --name company_info_field
python manage.py autocompleter_init --remove --store --name stock_info_field
python manage.py autocompleter_init --remove --store --name etf_info_field
python manage.py autocompleter_init --remove --store --name mutual_fund_info_field
python manage.py autocompleter_init --remove --store --name fund_info_field
python manage.py autocompleter_init --remove --store --name index_info_field
```

## Initialize Lists and Sets
You need to run Celery to actually get the lists you need!
```bash
celery -A ycharts worker -E -Q latestcalcs,main,alerts -l info
```

Now, while Celery is running, IN A NEW TAB, do this
```bash
python manage.py securities_process_lists_and_sets
```

## Install YCharts Node Packages

```bash
cd /sites/ycharts_node

# Set necessary flags
export PKG_CONFIG_PATH=/usr/X11/lib/pkgconfig
export LDFLAGS="$(pkg-config --libs cairo) $(pkg-config --libs pixman-1)"

# Install NPM packages
npm install
```

If you get some weird errors, try the following, then redo the above:

```bash
# Clear NPM cache
npm cache clean
# Remove all installed packages and other NPM stuffs
rm -rf ~/.npm
rm -rf /sites/ycharts_node/node_modules
```

> NOTE: Be sure you are not using `sudo`. You might need to change
> permissions for `~/.npm` and/or `/sites/ycharts_node/node_modules`
> to get `npm install` to work.

## Setup PDF -> Html Converter

```bash
# The script must be run from inside the ycharts_pdf_to_html directory
cd /sites/ycharts/binaries/ycharts_pdf_to_html/
# Run the build script for mac
./build.sh mac
```

## Install PhantomJS

```bash
# Run the install script from any directory for mac
/sites/ycharts/binaries/phantomjs/install.sh mac

# PhantomJS can now be run via
/sites/ycharts/binaries/phantomjs/bin/phantomjs
```

## Install wkhtmltox

```bash
# Run the install script from any direction for mac
sudo /sites/ycharts/binaries/wkhtmltox/install.sh mac
```

## Configure AWS CLI

> NOTE: Make sure you have run `dev_requirements.txt`. Packages `ply` and `awscli`
> must be installed.

Ask someone to create an IAM user for you. Once created, they should give you
your credentials as well as a temporary password. They will also set up multi-factor
authentication for you.

After logging in to [the AWS console](https://ycharts.signin.aws.amazon.com/console), add
your credentials to your `~/.bash_profile` or `~/.profile`.

```bash
export AWS_ACCESS_KEY_ID=<YOUR_ACCESS_KEY_HERE>
export AWS_SECRET_ACCESS_KEY=<YOUR_SECRET_ACCESS_KEY_HERE>
export AWS_DEFAULT_REGION=us-east-1
```

Apply the new settings:

```bash
source ~/.bash_profile
# Test that it works
aws help
```
