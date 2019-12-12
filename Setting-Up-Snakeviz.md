Setting Up SnakeViz
-------------------

[SnakeViz](https://jiffyclub.github.io/snakeviz/) is a great tool to visualize output from python's cProfile module when profiling code. This is short guide to get SnakeViz running in a Vagrant machine.


- Choose or create a directory to create a new Vagrant VM, and cd into that directory. 
  For this guide:
	```
	mkdir ~/Development/profiling && cd ~/Development/profiling
	```
- Create a directory to hold all the .prof files gathered from profiling code. `mkdir profiles`

- Create a new vagrant box: `vagrant init ubuntu/bionic64` which will create a Vagrantfile in the current directory

- Update the created Vagrantfile to sync the directory created above. Example:
	```
	config.vm.synced_folder "~/Development/profiling/profiles", "/home/vagrant/profiles"
	```
- Update the Vagrantfile generated to forward a port on your machine to the vagrant box. Example:
	```
	config.vm.network "forwarded_port", guest: 9000, host: 9000
	```
- Create the box: `vagrant up` and ssh into it: `vagrant ssh`
- Update apt, install pip, and install snakeviz:
	```sh
	sudo apt update && sudo apt install python3-pip && pip3 install snakeviz
	```

- Refresh the PATH so we can run snakeviz without the full path
	```
	source ~/.profile
	```
- Run snakeviz:
	```sh
	snakeviz -H 0.0.0.0 -s -p 9000 profiles/
	```

Now you can go to http://localhost:9000/snakeviz/ to view your profiles using snakeviz!