## Inside Vagrant:
```bash
# Go into the redis cli
redis-cli
# Delete everything in redis (this will stop VBox from maxing out its 8 GB of memory)
flushdb
```
## Outside Vagrant:
Add the following to your ``/sites/ycharts/ycharts/settings/development_local.py`` (create it if it doesn't exist):
```python
REDIS_CELERY_CONNECTION = {
   'host': '172.28.128.1',
   'port': 6379,
   'db': 0,
   # To receive strings not bytes from Redis in py3k.
   'decode_responses': True,
}

REDIS_AUTOCOMPLETER_CONNECTION = REDIS_CELERY_CONNECTION
REDIS_LISTS_AND_SETS_CONNECTION = REDIS_CELERY_CONNECTION
REDIS_MISC_CONNECTION = REDIS_CELERY_CONNECTION

# Redis sessions
SESSION_REDIS_HOST = REDIS_MISC_CONNECTION['host']
SESSION_REDIS_PORT = REDIS_MISC_CONNECTION['port']
SESSION_REDIS_DB = REDIS_MISC_CONNECTION['db']
SESSION_REDIS_PREFIX = 'session'

# Result store settings
CELERY_RESULT_BACKEND = 'redis://{host}:{port}/{db}'.format(**REDIS_CELERY_CONNECTION)

# Broker settings
BROKER_URL = 'redis://{host}:{port}/{db}'.format(**REDIS_CELERY_CONNECTION)

# Autocompleter settings
AUTOCOMPLETER_REDIS_CONNECTION = REDIS_AUTOCOMPLETER_CONNECTION
```
After that, reinstall redis to reset your configs:
```bash
brew uninstall redis28  # ignore this if you don't have redis at all outside vagrant
brew install redis28
# Symlink plist to launch redis on startup
ln -sfv /usr/local/opt/redis28/*.plist ~/Library/LaunchAgents
```
Open ``/usr/local/etc/redis28.conf`` in your favorite text editor.
Under ``GENERAL`` change the ``bind`` configuration to the following:
```conf
bind 0.0.0.0
```
For a performance boost, change the three uncommented lines under ``SNAPSHOTTING`` to be:
```conf
save 1800 1
save 600 10
save 300 10000
```
Exit the text editor and start redis:
```bash
launchctl load ~/Library/LaunchAgents/homebrew.mxcl.redis28.plist
```
Apply the changes to ``development_local.py`` above to vagrant:
```bash
vagrant reload --provision
```
## Inside Vagrant:
Finally, rerun lists and sets with the new redis:
```bash
yc_celery
# In a separate shell:
vagrant ssh
python manage.py securities_process_lists_and_sets
```
