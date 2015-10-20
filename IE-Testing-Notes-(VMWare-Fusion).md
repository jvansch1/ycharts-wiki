### Getting Started With VMWare Fusion

## Getting Started

### Step 1:

Break out the Ycharts Credit Card and go to [http://www.vmware.com/products/fusion/](http://www.vmware.com/products/fusion/)

You do not need Pro, it just has a bunch of workstation/IT management related garbage we do not need.

Download and install.

### Step 2 the Hard Way:
Go to [http://www.modern.ie/en-us/virtualization-tools#downloads](http://www.modern.ie/en-us/virtualization-tools#downloads)

On "get vms" select Mac and vmware fusion for mac.  Download the parts one at a time for each install you want.

### Step 2 the Easy Way:
You might want to do these one at a time (see last substep)
get XP ie8 in terminal:

    curl -O -L "https://www.modern.ie/vmdownload?platform=mac&virtPlatform=vmware&browserOS=IE8-WinXP&parts=0&filename=VMBuild_20131127/VMware/IE8_WinXP/Mac/IE8.WinXP.For.MacVMWare.sfx"

get win 7 ie9:

	$ curl -O "https://az412801.vo.msecnd.net/vhd/IEKitV1_Final/VMWare_Fusion/IE9_Win7/IE9.Win7.For.MacVMware.part0{1.sfx,2.rar,3.rar}"

From the terminal, allow the downloaded file to execute by typing the following on the SFX file only.

	chmod +x <filename>.sfx

Run the SFX file from a terminal 

	./<filename>.sfx 

It will decompress into the VMWAREVM you can open with VMWare Fusion.  This will take a minute.

Move the vmware vm somewhere stable (docs, some personal dev folder, whatever) and open with vmware fusion.

Note:  You will not be able to register these XP/Win7 licenses so do not get too comfortable in them.  They are strictly for testing IEs so you'll be able to log in, get on IE and go to the site, but otherwise, since we may need to redownload every 30 days (tbd), treat them as disposable.  Not actually closing your vm is a bit of trick around this but otherwise you will need to do all of these again eventually.

##Accessing your local via your VM:

Runserver in the following way.  This will allow you to access it from your vm (or elsewhere)

	python manage.py runserver 0.0.0.0:8000

Find your IP: Apple->system preferences->network

Underneath where it says "Status: Connected" you'll see:

	"<connection> is currently active and has the ip address XX.X.X.XX"

copy your ip into the browser window on your vm with :8000
like this: XX.X.X.XX:8000