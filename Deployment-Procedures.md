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

### Update your SSH! This changes frequently!
    cp confs/developers/ssh.conf ~/.ssh/config

### Release code to production
```bash
cd /sites/ycharts/

# Start the release
fab production build start_deploy

# Run migrations manually
ssh production_admin
python /sites/ycharts/manage.py migrate

# End the release
fab production end_deploy

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

> NOTE: The following will take down the site for a couple of seconds then will re-open
> the site in read-only mode (no creating watchlists for users, etc.) with a yellow
> box that notifies users we are preforming scheduled maintenance

```bash
fab production build quick_deploy

# If you are only releasing code ONLY to the admin server, run:
fab production:admin build quick_deploy

# If you are only releasing code ONLY to the web server, run:
fab production:web build quick_deploy
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
fab staging build start_deploy

# Run migrations manually
ssh staging_admin
python /sites/ycharts/manage.py migrate

# End the release
fab staging end_deploy

# Now do any post deploy tasks (see deployment notes)
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
