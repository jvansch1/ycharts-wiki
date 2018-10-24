# Copying Production DB to Staging

1. Go to [AWS RDS Snapshots](https://console.aws.amazon.com/rds/home?region=us-east-1#db-snapshots:) and find the latest production snapshot
1. Click **Restore Snapshot**
[![Screenshot from Gyazo](https://gyazo.com/532cc0441da0c16b1afaeb9f005c5ada/raw)](https://gyazo.com/532cc0441da0c16b1afaeb9f005c5ada)
1. Make sure all the settings are the same as the current staging database.
1. Click **Restore DB Instance**
1. Once the DB is running, update the host in ycharts/settings/staging.py
1. Do a staging release to test that the new DB is up and running
1. Once you have determined that the new DB is working fine, delete the old Staging DB