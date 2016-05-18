## Edit your sudoers file to not ask for password on vagrant ups

Use visudo! Use visudo! DO NOT edit sudoers manually, visudo checks syntax to prevent errors (if you mess up, its bad!)
```
sudo visudo
```

Add the lines to the bottom
```
Cmnd_Alias VAGRANT_EXPORTS_ADD = /usr/bin/tee -a /etc/exports
Cmnd_Alias VAGRANT_NFSD = /sbin/nfsd restart
Cmnd_Alias VAGRANT_EXPORTS_REMOVE = /usr/bin/sed -E -e /*/ d -ibak /etc/exports
%admin ALL=(root) NOPASSWD: VAGRANT_EXPORTS_ADD, VAGRANT_NFSD, VAGRANT_EXPORTS_REMOVE
```