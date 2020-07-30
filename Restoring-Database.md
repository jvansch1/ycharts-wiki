# Restoring Your Database

## Copy Data Files Method (Preferred)
#### Copy the Backup Files
Copy the `ycharts_db` folder from the DB Restore machine to your home directory (`~`)

#### Restore Your Database
> NOTE: By default, the script will move a database backup from `~/ycharts_db` to
> `/usr/local/var/mysql` and keep the current owner and group of `/usr/local/var/mysql`.
> These defaults should work if you have installed mysql via Homebrew.

```bash
# To run with default values (should work if mysql was installed via Homebrew)
/sites/ycharts/scripts/developers/restore_database.sh

# To copy instead of move files (THIS IS MUCH BETTER!!!!!!!)
/sites/ycharts/scripts/developers/restore_database.sh -c

# To specify a different source directory
/sites/ycharts/scripts/developers/restore_database.sh -s ~/ycharts_sql_backup_files

# To output usage information
/sites/ycharts/scripts/developers/restore_database.sh
# usage: /sites/ycharts/scripts/developers/restore_database.sh [OPTIONS]
#
# This script moves or copies backed up MySQL database files to the MySQL database folder
#
# OPTIONS:
#     -h Show this message and exit
#     -s Source directory of the database files (default: ~/ycharts_db)
#     -d Destination directory of the database files (default: /usr/local/var/mysql)
#     -c Copy the files from the source directory instead of moving them
#     -o Override the owner of the destination directory (defaults to current owner)
#     -g Override the group of the destination directory (defaults to current group)

```