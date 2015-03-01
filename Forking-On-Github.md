# Forking a project on github and developing on it.

## Fork the Project
    For the project Yourself

    Ask YCharts admin to fork the project for YCharts you want and give you write access to it.

## Go to right directory, and clone project

    cd /sites
    git clone git@github.com:YOUR_USER_NAMEin/PROJECT_NAME.git

## Go to directory and set up remotes

### Upstream remote:
    cd PROJECT_NAME
    git remote add upstream git://github.com/ORIGINAL_USER/PROJECT_NAME.git
    git remote add upstream git://github.com/wesm/pandas.git
    git pull upstream master

### Add yourself as remote and pull
    git remote add YOURNAME git@github.com:YOUR_USERNAME/PROJECT_NAME.git
    git pull YOURNAME master
    git push YOURNAME master

## Add ycharts as remote and pull
    git remote add ycharts git@github.com:ycharts/PROJECT_NAME.git
    git pull ycharts master
    git push ycharts master

## Make sure project is already installed
    cd /sites/ycharts
    workon ycharts
    pip install pandas

## COMPILED PACKAGES: Use checkout version of library instead of installed version
    cd ~/.virtualenvs/ycharts/lib/python2.6/site-packages

If the package exists, delete it
    rm -rf PROJECT_NAME

Now compile and install it (as long as your are on the ycharts virtualenv packaged will go in the right place)_

    cd /sites/PROJECT_NAME
    python setup.py install

## SOURCE PACKAGES: Use checkout version of library instead of installed version
    cd ~/.virtualenvs/ycharts/src

    If the package exists, delete it
        rm -rf PROJECT_NAME

    Link the git copy to the virtual env
        ln -s -f /sites/PROJECT_NAME PROJECT_NAME
