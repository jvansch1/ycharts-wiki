## Overview

This guide goes over the necessary process to switch our IP addresses across staging and production. The general steps are as follows:

[1.](https://github.com/ycharts/ycharts/wiki/IP-Switch-Guide#1-allocate-new-ip-address) Allocate new IP addresses.

[2.](https://github.com/ycharts/ycharts/wiki/IP-Switch-Guide#2-notify-organizations-of-new-ips) Send list of new IPs to relevant organizations so they can whitelist them first (tell them there is 1 month sunset period).

[3.](https://github.com/ycharts/ycharts/wiki/IP-Switch-Guide#3-associate-new-ips-with-nat-servers) Once we confirm that they are whitelisted, switch out the IP for staging and make sure it works for about a week. Then one by one associate the IPs to our NAT servers in production once it works on staging.

[4.](https://github.com/ycharts/ycharts/wiki/IP-Switch-Guide#4-update-various-places-with-our-new-ips) Update IP addressed in ssh config

***

### 1. Allocate new IP Address
1. Login to AWS
2. Select "EC2"
<br>![](https://www.diigo.com/file/image/sosopsazdccdpappdzcdppqbor/AWS+Management+Console.jpg)
3. Select "Elastic IPs"
<br>![](https://www.diigo.com/file/image/sosopsazdccdpasebzcdppqbpq/EC2+Management+Console.jpg)
4. Allocate 3 New IP addresses by selecting "Allocate New Address" and Make sure to select used in "VPC"
<br>![](https://www.diigo.com/file/image/sosopsazdccdsdpcozcdpprbpr/EC2+Management+Console.jpg)


### 2. Notify organizations of new IPs
Send an email to the below organizations to whitelist our 3 new IPs that were allocated.

```
We are making system configuration changes, and I need to add 3 IPs to the whitelist to allow access to the Stocktwits/Morningstar/Dallas Fed... We will be sunsetting 3 IPs of our existing IPs in about 1 month, so we are really just swapping them.

The IPs I need to add are:
52.6.96.159
52.5.69.58
52.4.31.32

Can you direct me towards who can help me with this?
```

1. Morningstar (realtimehelpdesk@morningstar.com)
1. Bank of Dallas (dal.webmaster@dal.frb.org)
1. Bloomberg (indexops@bloomberg.net) (For Index data FTP)
1. MyPrivateProxy - whitelisted IPs for no user/password auth


### 3. Associate new IPs with NAT servers
1. In `ssh.conf` update each entry to the correct IP Address 
