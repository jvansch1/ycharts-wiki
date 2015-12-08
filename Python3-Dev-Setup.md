# To run YCharts Codebase on Py3k environment.

### Install python3
Go [here](https://www.python.org/downloads/release/python-340/) and download "Mac OS X 64-bit/32-bit" installer.
The reason why we do NOT do `brew install python3` because it will get the latest stable python 3.5 release which does NOT support Django 1.7.7.

### Create new virtualenv
```bash
deactivate
mkvirtualenv -p /Library/Frameworks/Python.framework/Versions/3.4/bin/python3.4 ycharts3
```
Note how we specified a custom python3.4 path.

### Start working on ycharts3 env
```bash
workon ycharts3
```

### Test that it's correct python version
```bash
python --version
# Python 3.4.3
```

### Run all requirements
```bash
pip install -r /sites/ycharts/confs/server/requirements.txt
pip install -r /sites/ycharts/confs/server/dev_requirements.txt
```

### Other Python3 changes
 In `ycharts/settings/active.py` change

`from development import *` to `from .development import *`

### Couple notes
`fabric` doesn't support python3 so we should fix it.

### Now you are all set to run the django server and enjoy our codebase on py3k. Boom!