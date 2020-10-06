## Steps

- Download Cyberduck here: https://cyberduck.io/download/
- Open Cyberduck and go to "Open Connection", make sure it is set to "FTP (File Transfer Protocol)", and enter the server, username, and password for the FTP you would like to connect to.
- FTP credentials can be found in `/sites/ycharts/ycharts/settings/secrets.py`.
- For example if attempting to connect to Morningstar FTP, the FTP credentials can be found in `/sites/ycharts/ycharts/settings/secrets.py` as  `MORNINGSTAR_FTP_HOST`, `MORNINGSTAR_FTP_LOGIN`, and `MORNINGSTAR_FTP_PASSWORD`.