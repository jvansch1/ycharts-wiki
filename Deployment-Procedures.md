### YCharts Deployment Procedures

## Testing deployment setup
```bash
cd /sites/ycharts
fab staging ping
```

## Releasing to production

### Prepare release to production
```bash
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
```

### Release code to production
```bash

# Update your ssh files
cp confs/developers/ssh.conf ~/.ssh/config

cd /sites/ycharts/

# Start the release
fab production build stop deploy

# Run migrations manually
ssh production_admin
python /sites/ycharts/manage.py migrate

# End the release
fab production start

# Now do any post deploy tasks
```

### Prepare e-mail to notices@ycharts.com
Include all major features and whatnot

### Make annotation on Google Analytics
Include release number and basic description

## Doing a hotfix release on production

### Branch code
```bash
git checkout master
git pull origin master
```

Now you make the changes you want

### Commit code and tag
```bash
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
```

### Doing a quick release on production

> NOTE: The following goes through servers one-by-one, removing them from the load
> balancer, deploying, then adding them back. There should be no downtime.

```bash
fab production build hotfix

# If you are only releasing code ONLY to the admin server, run:
fab production:admin build hotfix

# If you are only releasing code ONLY to the web server, run:
fab production:web build hotfix
```


## Releasing to Staging

### Quick deploy to staging
```bash
fab staging build quick_deploy

# Machine specific quick deploy
fab staging:web build quick_deploy
fab staging:admin build quick_deploy
fab staging:indicators_admin build quick_deploy
```

### Prepare release to staging
```bash
cd /sites/ycharts
git checkout develop
git pull origin develop
git push origin develop

cd /sites/ycharts_node
git checkout develop
git pull origin develop
git push origin develop
```


### Releasing code to staging
```bash
cd /sites/ycharts/

# Start the release
fab staging build stop deploy

# Run migrations manually
ssh staging_admin
python /sites/ycharts/manage.py migrate

# End the release
fab staging start

# Now do any post deploy tasks (see deployment notes)
```

## Various Fab Options
Setting the environment takes a couple options:

```bash
# To run a single role
fab staging:admin

# To run multiple roles
fab staging:'admin\,web'

# To skip git and environment checks (for automated runs like queue machines)
fab staging:skip=git_checks

# To run on a single host
fab staging:'web|10.1.279'
```

Build can also take several options:

```bash
# To skip generating menu json files
fab staging build:skip=menus

# To skip running autocompleter inits
fab staging build:skip=autocompleters

# To skip running collectstatic
fab staging build:skip=staticfiles

# To skip running all management commands
# (i.e., only pull code, install packages, and tar code)
fab staging build:skip='menus\,autocompleters\,staticfiles'
```

## Running a One-off Command on Every Machine
To run a one-off command on every machine, run the following:

```python
# On staging
fab staging run_command:[command]

# On production
fab production run_command:[command]

# As sudo
fab staging run_command:[command],sudo
```

## Optional deployment commands
To see a list of available commands

```bash
fab --list
```
