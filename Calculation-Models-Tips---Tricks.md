# How to reset a model back to auto increment of 1
```
# Disable autoscaling and enable hyper scaling before doing this to speed things up

fab production disable_autoscaling
fab production enable_autoscaling:hyperscale=hyperscale

# ssh into production
vagrant ssh
ssh production_admin

# Login into mysql process
control + r, type in mysql, hit enter

# Type MySQL Password (find in production.py)
**********

# Make sure you use the right database
use ycharts;

# CHANGE THE TABLE NAME TO WHAT YOU WANT FIXED
truncate calculations_mutualfunddailyperformancecalc;
ALTER TABLE calculations_mutualfunddailyperformancecalc AUTO_INCREMENT = 1;

# The below should output 1 if done correctly
select auto_increment from information_schema.tables where table_name = 'calculations_mutualfunddailyperformancecalc';

# Reset the model from
companies_tasks_run, indices_tasks_run, mutual_funds_tasks_run to get the historical data back and running

# Disable hyper scaling when done, put it back in normal scaling mode
fab production disable_autoscaling enable_autoscaling 


