# Upgrading Vagrant Developer Box to Ubuntu 18.04

This guide is meant to help you switch over from your Ubuntu 14.04 Vagrant developer box ot the new Ubuntu 18.04 Vagrant developer box. I suggest having two terminal tabs open - one tab viewing the `ycharts` directory and the other the `ycharts_systems` directory. I'll use `ycharts` and `ycharts_systems` to refer to these two tabs throughout this document.

First things first, in both tabs, you're going to want to make sure you are up to date with the develop branch.

`ycharts` and `ycharts_sytems`:
```sh
git checkout develop
git pull origin develop
```

Now make sure we are working on the ycharts_systems virtual environment in the ycharts terminal, and let's make sure the existing vagrant machine is turned off.
`ycharts`:
```sh
workon ycharts_systems
vagrant halt default
```

Now install requirements.txt to update our packages in ycharts_systems.
`ycharts_systems`:
```sh
pip install -r requirements.txt
```

Last step before we provision the new developer box is to delete the old network configuration created by VirtualBox. To do this:
```
Open Virtual Box -> Preferences -> Network -> Host-only Networks -> Delete the vboxnet0 network
```

Now you should be able to provision the new developer box:
```
vagrant up bionic --provision --run_initial_setup
```

Once that finishes running, test it out by sshing into the new Vagrant box and running the Django server.
`ycharts`:
```sh
vagrant ssh bionic
yc_django
```