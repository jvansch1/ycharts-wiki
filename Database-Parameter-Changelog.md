Below are changes that have been made to the production database's [AWS Parameter Group](https://console.aws.amazon.com/rds/home?region=us-east-1#parameter-groups-detail:ids=ycharts5-7;type=DbParameterGroup;editing=false). 

2021 - 02 - 06
--------------
- Upgrade Database to have 10,000 provisioned IOPS from 8,000 IOPS
  - This was done because our write-intensive workloads were pushing our IOPS near 8,000 consistently. 
- `innodb_io_capacity_max` changed from `4000` to `6000`.
  - Write-intensive workloads can cause buffer pool flushing activity to fall behind, in which case InnoDB will flush more aggressively. This parameter defines the IOPS dedicated to these background tasks such as flushing in these scenarios. We are increasing this parameter so that the database can quickly catch up on flushing activity if it does happen to fall behind but keeping it significantly below the provisioned IOPS of 10,000 so we don't take up too much I/O work from non-innodb workloads.
- `innodb_io_capacity` changes from `1000` to `2000`.
  - We wanted a sensible value that allows for innodb to have enough IOPS to handle a normal workload when flushing the buffer pool but keeping it below the max of `6000`
- `innodb_flush_sync` changes from the default of `1` (ON) to `0` (OFF)
  - While the `innodb_io_capacity_max` value represents the max IOPS that innodb can use, in practice innodb can and will take more IOPS than `innodb_io_capacity_max` if it feels it needs to. When innodb goes above the max this is usually called a "flush storm" and it caused an [outage for us in January](https://docs.google.com/document/d/1Uzm5NQP0Y9hfZG_ctyX3txykwdupNrjkCASy4Iv3vL8/edit#). You can also [read more here](https://www.percona.com/blog/2020/01/22/innodb-flushing-in-action-for-percona-server-for-mysql/) about flush storms and why they happen. What is important is that when this is set to `0` (OFF) that innodb __will not__ go above the `innodb_io_capacity_max` of `6000`.

2021 - 01 - 29
--------------
- `general_log` changed from `1` to `0`.
  - We found the general log of queries to not be very helpful and not worth the cost of having it enabled.

2021 - 01 - 27
--------------
- `innodb_io_capacity_max` changed from `2000` to `4000`.
  - Write-intensive workloads can cause buffer pool flushing activity to fall behind, in which case InnoDB will flush more aggressively. This parameter defines the IOPS dedicated to these background tasks such as flushing in these scenarios. We are increasing this parameter so that the database can quickly catch up on flushing activity if it does happen to fall behind.

2020 - 12 - 28
--------------
- `general_log` changed from the default of `0` to `1`. 
  - This was changed to allow us to enable cloudwatch logging from the Slow Query, General, and Audit logs. [AWS Instructions](https://aws.amazon.com/premiumsupport/knowledge-center/rds-aurora-mysql-logs-cloudwatch/) 
- `slow_query_log` changed from the default of `0` to `1`.
  - This was changed to allow us to enable cloudwatch logging from the Slow Query, General, and Audit logs. [AWS Instructions](https://aws.amazon.com/premiumsupport/knowledge-center/rds-aurora-mysql-logs-cloudwatch/) 


2020 - 12 - 2
--------------
Below are the changes made on December 2nd, 2020. A more in-depth analysis of the changes can be found in this gist which also describes the issue that we were solving in more detail. [Gist Link](https://gist.github.com/KFoxder/5373bd47504feba39148d4a661fc2b92)

- `innodb_buffer_pool_size` changed  from 75% of available memory (`{DBInstanceClassMemory*3/4}`) to 80% of available memory (`{DBInstanceClassMemory*4/5}`). 
  - This was done since having more memory allocated to the buffer potentially would lower the cleaning that was causing locks and because MySQL documentation recommends that "On dedicated servers, up to 80% of physical memory is often assigned to the buffer pool." [MySQL Doc Ref](https://dev.mysql.com/doc/refman/5.6/en/innodb-buffer-pool.html)
- `innodb_purge_threads ` changed from the default of `4` to `8`
  - This was done so that our purge threads are the same size as our buffer pool (which is 8 by default). Therefore there is always a thread cleaning a buffer pool lowering the chances of a lock from page cleaning. MySQL recommends that "If DML action is concentrated on a single table or a few tables, keep the setting low so that the threads do not contend with each other for access to the busy tables. If DML operations are spread across many tables, increase the setting." [MySQL Doc Ref](https://dev.mysql.com/doc/refman/5.7/en/innodb-purge-configuration.html)
- `innodb_lru_scan_depth` changed from the default of `1024` to `256`. 
  - This was changed to an experiment with a lower value which means that the page cleaners do not perform as exhaustive search which means there is less chance for a lock occurring during cleaning.
- `innodb_flush_neighbors` changed from the default of `1` to `0` which disables flushing of neighbors.
  - This is recommended for SSD drives which our AWS database is on. [MySQL Doc Ref](https://dev.mysql.com/doc/refman/5.7/en/innodb-buffer-pool-flushing.html)
- `innodb_change_buffering` changed from the default of `all` to `none`. 
  - This is recommended by AWS. "We recommend that you disable change buffering by setting the value to none because all causes extremely long shutdown times for upgrades. This feature was useful in the past with slow disks, but isn’t useful now." [AWS Best Practices Docs](https://aws.amazon.com/blogs/database/best-practices-for-configuring-parameters-for-amazon-rds-for-mysql-part-1-parameters-related-to-performance/)
- `innodb_io_capacity` changed from the default to `1000`.
  - This is recommended by AWS. [AWS Best Practices Docs](https://aws.amazon.com/blogs/database/best-practices-for-configuring-parameters-for-amazon-rds-for-mysql-part-1-parameters-related-to-performance/)
- `innodb_io_capacity_max` changes from the default to `2000`.
  - Recommended to be double that of `innodb_io_capacity`. [AWS Best Practices Docs](https://aws.amazon.com/blogs/database/best-practices-for-configuring-parameters-for-amazon-rds-for-mysql-part-1-parameters-related-to-performance/)

2020 - 11 - 29
---------
- `log_output` changed from the default of `TABLE` to `FILE`. 
  - This was changed to allow us to enable cloudwatch logging from the Slow Query, General, and Audit logs. [AWS Instructions](https://aws.amazon.com/premiumsupport/knowledge-center/rds-aurora-mysql-logs-cloudwatch/) 


2019
---------
- `innodb_print_all_deadlocks` was changed from the default of `0` to `1`, which enables it.
  - This was changed because we have a detailed log that we capture of the database and thus wanted deadlocks to be printed as part of the log statements.


2018
---------
- `connect_timeout` changed to `3600`. 
  - We use this value because it is the longest that we allow a web request to persist and thus we don't want to drop the DB connection if a valid web request is still processing. 
- `lock_wait_timeout` changed from the default to `300`.
  - This was changed to be a higher value than the default since we have many insertions into the same tables when processing data that require a longer period so that they can succeed and not timeout. 
- `max_allowed_packet` changed from the default to `33554432`.
  - This was changed because we sometimes have log output that we write to the DB that can be very large. 