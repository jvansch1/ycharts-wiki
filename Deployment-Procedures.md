### YCharts Deployment Procedures

## Installing Fabric
Note: This will automatically install paramiko and pycrypto to support SSH connecting through Python

    pip install --upgrade fabric

## Testing deployment setup
    cd /sites/ycharts
    fab staging admin.ping

## Releasing to production

### Prepare release to production
    cd /sites/ycharts
    git checkout develop
    git pull origin develop
    git checkout master
    git pull origin master
    git merge --no-ff develop
    git fetch origin --tags
    git tag X.X.X
    git push origin master
    git push origin --tags

    cd /sites/ycharts_node
    git checkout develop
    git pull origin develop
    git checkout master
    git pull origin master
    git merge --no-ff develop
    git fetch origin --tags
    git tag X.X.X
    git push origin master
    git push origin --tags

### Update your SSH! This changes frequently!
    cp confs/developers/ssh.conf ~/.ssh/config

### Release code to production
    cd /sites/ycharts/

    # Run the build step
    fab production build

    # Start the release
    fab production start_deploy

    # Run migrations manually (safe) or with fab (can fail on long migrations)
    ssh ycharts_admin
    python /sites/ycharts/manage.py migrate
    # OR
    fab production admin.run_migrations

    # End the release
    fab production end_deploy

    # Now do any post deploy tasks

### Prepare e-mail to notices@ycharts.com
Include all major features and whatnot

### Make annotation on Google Analytics
Include release number and basic description

## Doing a hotfix release on production

### Branch code
    git checkout master
    git pull origin master

Now you make the changes you want

### Commit code and tag
    git commit
    git fetch origin --tags
    git tag X.X.X
    git push origin master
    git push origin --tags
    git checkout develop
    git pull origin develop
    git merge --no-ff master
    git push origin develop
    git checkout master

## Doing a quick release on production

(The following will take down the site for a couple of seconds then will re-open
the site in read-only mode (no creating watchlists for users, etc.) with a yellow
box that notifies users we are preforming scheduled maintenance)

### If you are releasing code to admin and web, run:

    fab production quick_deploy

### If you are only releasing code to the admin server, run:

    fab production admin.quick_deploy

Careful with this - be sure that you *only* made changes to code that will affect admin.

### If you are only releasing code to the web server, run:

    fab production web_quick_deploy

Again, be careful here - be sure that you *only* made changes to code that affects the web server.

## Releasing to Staging

### Quick deploy to staging
    fab staging quick_deploy

### If you are only doing a machine specific quick_deploy

    fab staging web_quick_deploy
    fab staging queue_quick_deploy
    fab staging admin.quick_deploy
    fab staging indicators_admin.quick_deploy

### Prepare release to staging
    cd /sites/ycharts
    git checkout develop
    git pull origin develop
    git push origin develop

    cd /sites/ycharts_node
    git checkout develop
    git pull origin develop
    git push origin develop


### Releasing code to staging
    cd /sites/ycharts/

    # Run build step
    fab staging build

    # Start the release
    fab staging start_deploy

    # Run migrations manually (safe) or with fab (can fail on long migrations)
    ssh ycharts_staging_admin
    python /sites/ycharts/manage.py migrate
    # OR
    fab staging admin.run_migrations

    # End the release
    fab staging end_deploy

    # Now do any post deploy tasks (see deployment notes)
 

## Running a One-off Command on Every Machine
To run a one-off command on every machine, run the following:

    fab [env] run_command:[command]

where `[env]` is the environment and `[command]` is the desired command. It is run from `env.ycharts[_node]_web_root`.

To run as `sudo`:

    fab [env] run_command:[command],sudo=yes

To run on a single machine or class of machines:

    fab [env] admin.run_command:[command]

## Optional deployment commands
To see a list of available commands

    fab --list

