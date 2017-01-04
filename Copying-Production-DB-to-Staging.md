# Copying Production DB to Staging

1. Go to RDS Snapshots and find the latest production snapshot
1. Click **Restore Snapshot**
1. Change the following settings:
    - **DB Instance Class**: r3.xlarge
    - **Multi-AZ Deployment**: No
    - **DB Instance Identifier**: staging-YYYY-MM-DD
    - **VPC**: staging
    - **Subnet Group**: staging_db_subnet_group
    - **Publicly Accessible**: No
    - **Availabilitiy Zone**: us-east-1d
1. Click **Restore DB Instance**
1. Once the DB is running, update the host in ycharts/settings/staging.py
1. Do a staging release to test that the new DB is up and running
1. Once you have determined that the new DB is working fine, delete the old Staging DB