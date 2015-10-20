# VirtualBox
If you need to test on other IE versions, ie. reports of a user saying IE x.x doesn't work on Windows x.x 

go here
```
https://dev.modern.ie/tools/vms/windows/
```

download the correct version. Select the VirtualBox version. You may need to install VirtualBox at 
```
https://www.virtualbox.org/wiki/Downloads
```

In VirtualBox, go to File, Import Appliance

![](https://d1zjcuqflbd5k.cloudfront.net/files/acc_134337/1huKa?response-content-disposition=inline;%20filename=Screen%20Shot%20on%202015-10-20%20at%2011%3A36%3A30.png&Expires=1445355732&Signature=KVheW63B623NFBqxuwgxYL~H0tINK7R8bYXPDu6vLR-D9PT7udl1K8FFKVXFIzvmkTwWHs0y2JimzUQFza6dDrv7iqIEC6D64NHNirNk0JG9O6QBThMDH21N5tFtVzXjqbWu-SIaLfSwkJ3f6dEl0~XACELWgQuy3aGJrlRE6X4_&Key-Pair-Id=APKAJTEIOJM3LSMN33SA)

Select where you downloaded the VirtualBox Container, hit okay. You should have a final prompt of settings. Leave those as default (unless you know what you're doing).

![](https://d1zjcuqflbd5k.cloudfront.net/files/acc_134337/1k85H?response-content-disposition=inline;%20filename=Screen%20Shot%20on%202015-10-20%20at%2011%3A38%3A33.png&Expires=1445355819&Signature=gP0ZnTK~ca-MPMWOh5bowiS96-ofdNBXM84NnuTR9MS2VSWZ0c4dLge11FambdDjBVwiFWN-OFHVNM~bhJKm9uHWKV3xL6A1asmVpp7bPaCmyy3Iz8N7nLtFtvHeqcAM4ZPjXNINB7W4Sj8uLJ6BbQu7wKv3u8LzJev458srOoo_&Key-Pair-Id=APKAJTEIOJM3LSMN33SA)

# VMWare Fusion

### Step 1:

Break out the Ycharts Credit Card and go to [http://www.vmware.com/products/fusion/](http://www.vmware.com/products/fusion/)

You do not need Pro, it just has a bunch of workstation/IT management related garbage we do not need.

Download and install.

### Step 2:
Go to [http://www.modern.ie/en-us/virtualization-tools#downloads](http://www.modern.ie/en-us/virtualization-tools#downloads)

On "get vms" select Mac and vmware fusion for mac.  Download the parts one at a time for each install you want.

# Accessing your local environment via your virtual machine:

Runserver in the following way.  This will allow you to access it from your vm (or elsewhere)

	python manage.py runserver 0.0.0.0:8000

Find your IP: Apple->system preferences->network

Underneath where it says "Status: Connected" you'll see:

	"<connection> is currently active and has the ip address XX.X.X.XX"

copy your ip into the browser window on your vm with :8000
like this: XX.X.X.XX:8000