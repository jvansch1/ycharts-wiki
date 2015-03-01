## Fabric Tasks for Database Dumps

The commands on this page have been automated with the following fabric tasks:

To create a database dump file on staging/production:

    fab -i ~/.ssh/ycharts-2014-01.pem staging dump_db

To download the database dump file after creating it with the previous task:

    fab -i ~/.ssh/ycharts-2014-01.pem staging download_db

To restore the database dump to your local database, after downloading it with the previous task:

    fab restore_db

## Manual Backup Database

Log in to production_admin or staging_admin and do this

    cd /mnt/db_dumps
    mysqldump -h HOST -u USERNAME -p DBNAME > DUMPNAME.sql

Log out and now on your local machine do this

    cd ~
    scp -i ~/.ssh/ycharts-2014-01.pem <production_admin OR staging_admin>:/mnt/db_dumps/DUMPNAME.sql

## Manual Restore Database

First drop the old database and create a new one. Be very careful! Make sure you're in your local environment when running this!

    mysql -u root

    show databases;

    drop database ycharts;

    CREATE DATABASE ycharts CHARACTER SET utf8 COLLATE utf8_general_ci;
    GRANT ALL ON ycharts.* to 'ycharts'@'localhost' IDENTIFIED BY 'ycharts';
    FLUSH PRIVILEGES;

Now restore the DB from the dump

    cd ~
    Pattern : mysql -u USERNAME -p -D DBNAME < DUMPNAME.sql
    Most likely you will run ...
    mysql -u root -D ycharts < ycharts_all.sql


## Backup and Restore a Single Database Table

Backup:

    mysqldump -u ycharts -p ycharts TABLE_NAME > TABLE_NAME.sql

Restore:

    mysql -u ycharts -p ycharts < TABLE_NAME.sql

## Uninstalling mysql from OSX

May you never have to do this, but just in case:

    sudo rm /usr/local/mysql
    sudo rm -rf /usr/local/mysql*
    sudo rm -rf /Library/StartupItems/MySQLCOM
    sudo rm -rf /Library/PreferencePanes/MySQL*

Now, open your etc hostconfig and remove "MYSQLCOM=-YES-"

    vim /etc/hostconfig
    rm -rf ~/Library/PreferencePanes/MySQL*
    sudo rm -rf /Library/Receipts/mysql*
    sudo rm -rf /Library/Receipts/MySQL*
    sudo rm -rf /var/db/receipts/com.mysql.*

Now you are free to reinstall from the dmg and source your existing bash_profile (assuming it was setup correctly in the first place)
