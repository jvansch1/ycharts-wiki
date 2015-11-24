# To run Ycharts on Py3k environment.

### Install python3
```bash
brew update
brew install python3
```

### Create new virtualenv
```bash
deactivate
mkvirtualenv -p /usr/local/bin/python3 ycharts3
```

### Test that it's correct python version
```bash
python --version
```
# Python 3.4.3

### Start working on ycharts3 env
```bash
workon ycharts3
```

### Run all requirements
```bash
pip install -r /sites/ycharts/confs/server/requirements.txt
pip install -r /sites/ycharts/confs/server/dev_requirements.txt
```

### Couple notes

Currently `static_sitemaps` and `fabric` do not support python3 so
you should wait until we find replacement for `static_sitemaps` and make `fabric`
compatible with Python3.
