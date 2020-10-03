Every week we dump our most recent staging data to a S3 bucket so that developers can always have access to up to date data. 

**Prerequisites:**
- `gtar` is installed on your local development machine
- `zstd` is installed on your local development machine
- `ycharts_systems` repository is checked out locally and setup

#### 1. Download the database dump file from S3
Execute the following commands to download the compressed S3 file to your local environment. 
> It is important to note that it will take around 2:30 - 3 hours to download depending on your internet speed.

```
cd /sites/ycharts_systems
git checkout develop
git pull origin develop
./ycharts_download_dump
```

#### 2. Decompress the dump file
Execute the following commands to decompress the S3 file into a directory.
> This should take around 45 minutes to complete.
```bash
cd ~
gtar -xaf ycharts_db.tar.zst
rm ~/ycharts_db.tar.zst
rm -rf ~/ycharts_db
mv ~/mysql ~/ycharts_db
```

#### 3. Restore local DB with dumped database
To restore your local DB we need to copy the files from your root directory to the working directory of MySQL.
```bash
# To copy files from ~/ycharts_db to /usr/local/var/mysql
/sites/ycharts/scripts/developers/restore_database.sh -c
```