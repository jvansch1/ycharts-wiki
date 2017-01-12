### How to clean migrations

#### Requirements
1. All Databases (local, staging and production) are in sync. Meaning they have the same exact migrations applied, tables and schemas. This means the best time to do it is after a full release where or develop and master branches are the same.
1. Developers do not push to develop during the code freeze
1. Migrations that are not applied must be rewritten.

#### Steps

1. In every app's `migrations` folder delete all the migrations except for the `__init__.py` 
    1. ```find . -path "*migrations*" -name "*.py" -not -name "__init__.py" -type f -delete```
    1. Remove the X in the command above
1. Locally run `mysql -u ycharts -p ycharts -A ycharts` and then `delete from django_migrations;`
1. Run `python /sites/ycharts/manage.py makemigrations`
1. Run `python /sites/ycharts/manage.py migrate --fake`
1. Push this to `develop`
1. On Staging:
  1. Run `fab staging build stop deploy`
  1. Log into the staging mysql db by `ssh staging_admin` and then `mysql -u ycharts -A -p er45qw -h staging-2016-10-17.cy4wtovspprb.us-east-1.rds.amazonaws.com`
  1. Run `delete from django_migrations;`
  1. Run `python /sites/ycharts/manage.py migrate --fake`
  1. Run `fab staging start`
  
1. Let staging run for a day to make sure everything looks good and nothing breaks. In the mean time there is a code freeze. No one is allowed to push to `master` or `develop`.

1. On Production (after it runs on staging for at least 1 day):
  1. Prepare a [full release](https://github.com/ycharts/ycharts/wiki/Deployment-Procedures#prepare-release-to-production) to production
  1. Run `fab production build stop deploy`
  1. Log into the production mysql db by `ssh production_admin` and then `mysql -u ycharts -A -p er45qw -h production-2015-05-16.cy4wtovspprb.us-east-1.rds.amazonaws.com`
  1. Run `delete from django_migrations;`
  1. Run `python /sites/ycharts/manage.py migrate --fake`
  1. Run `fab production start`
