
## Copy Data Files Method (Preferred)
#### Copy the Backup Files
Copy the `ycharts_redis` folder from the Mac Mini to your home directory (`~`)

#### Restore Redis
> NOTE: By default, the script will move a database backup from `~/ycharts_redis` to
> `/usr/local/var` and keep the current owner and group of `/usr/local/var`.

```bash
# To run with default values (should work if mysql was installed via Homebrew)
/sites/ycharts/scripts/restore_redis.sh

# To copy instead of move files
/sites/ycharts/scripts/restore_redis.sh -c

# To specify a different source directory
/sites/ycharts/scripts/restore_redis.sh -s ~/ycharts_redis_backup_files

# To output usage information
/sites/ycharts/scripts/restore_redis.sh -h
# usage: /sites/ycharts/scripts/restore_redis.sh [OPTIONS]
#
# This script moves or copies backed up Redis database files to the Redis data folder.
# 
# OPTIONS:
#     -h  Show this message and exit.
#     -s  Source directory of the database files (default: ~/ycharts_redis)
#     -d  Destination directory of the database files (default: /usr/local/var)
#     -c  Copy the files from the source directory instead of moving them
#     -o  Override the owner of the destination directory (defaults to current owner)
#     -g  Override the group of the destination directory (defaults to current group)
```