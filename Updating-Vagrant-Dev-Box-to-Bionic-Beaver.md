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

Make sure you have the latest versions of both Ansible and Vagrant.
- [Vagrant 2.1.2](https://releases.hashicorp.com/vagrant/2.1.2/vagrant_2.1.2_x86_64.dmg)
- [Virtualbox 5.2.16](https://download.virtualbox.org/virtualbox/5.2.16/VirtualBox-5.2.16-123759-OSX.dmg)


Now install requirements.txt to update our packages in ycharts_systems.
`ycharts_systems`:
```sh
pip install -r requirements.txt
```

If running into an SSL CERT error when trying to install requirements, try the following in `ycharts_systems`:
```sh
curl https://bootstrap.pypa.io/get-pip.py | python
```
Then try pip installing again.

Last step before we provision the new developer box is to delete the old network configuration created by VirtualBox. To do this:
```
Open Virtual Box -> Global Tools (top right hand side) -> Delete the vboxnet0 network
```

Now you should be able to provision the new developer box:
```
vagrant up bionic --provision
```

Once that finishes running, test it out by sshing into the new Vagrant box and running the Django server.
`ycharts`:
```sh
vagrant ssh bionic
yc_django
```

Then the next time you want to start up the new vagrant dev box, the command will just be:
```sh
vagrant up bionic
```