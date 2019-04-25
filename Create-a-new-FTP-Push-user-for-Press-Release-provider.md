Steps to create a new user on our FTP Push server `ftp-push.ycharts.com`.
1. Come up with a username and password for the Press Release provider. Write them down.
2. SSH into the FTP Push server by finding the Private IP in the AWS Console. Example: `ssh -o StrictHostKeyChecking=no -o ProxyCommand='ssh ec2-user@52.206.94.48 -W %h:%p' ubuntu@10.0.1.144`
3. Once you are in the machine run the following commands:
```
sudo adduser --home /ftp_root/ychartsftp/[PRESS_RELEASE_PROVIDER_NAME] --gid 1001 --gecos "" --shell "" [PRESS_RELEASE_PROVIDER_NAME]
# You will be prompted to enter a password. Enter the password you wrote down in step 1
sudo mkdir /ftp_root/ychartsftp/[PRESS_RELEASE_PROVIDER_NAME]/press_releases
sudo chown -R [PRESS_RELEASE_PROVIDER_NAME]:vsftpd /ftp_root/ychartsftp/[PRESS_RELEASE_PROVIDER_NAME]
sudo chmod a-w /ftp_root/ychartsftp/[PRESS_RELEASE_PROVIDER_NAME]
```
4. Test that it is working by using the credentials and logging in as that user to the FTP. Use cyberduck or your preferred FTP client. 
5. Send the following credentials in an email to the Press Release provider contact.
```
FTP Credentials:
server: ftp-push.ycharts.com
port: 21
username: [PRESS_RELEASE_PROVIDER_NAME]
password: [LINK TO https://onetimesecret.com/ with Password]
```

