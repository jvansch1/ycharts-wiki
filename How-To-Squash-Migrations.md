### How to squash migrations

#### Requirements
1. All Databases (local, staging and production) are in sync. Meaning they have the same exact migrations applied, tables and schemas. This means the best time to do it is after a full release where or develop and master branches are the same.
1. Developers do not push to develop during the code freeze
1. Migrations that are not applied must be rewritten.

### Steps

#### 1. Create delete old migrations and create new on develop
1. In every app's `migrations` folder delete all the migrations except for the `__init__.py` 
    1. ```find . -path "*migrations*" -name "*.py" -not -name "__init__.py" -type f -delete```
1. Delete the `django_migrations` rows
    1. ```mysql -u ycharts -pycharts -A ycharts```
    1. ```delete from django_migrations;```
1. Run `python /sites/ycharts/manage.py makemigrations`
1. Run `python /sites/ycharts/manage.py migrate --fake`
1. Commit and push this to `develop`
    1. ```git add . && git commit -am "Squash migrations." && git push origin develop```

#### 2. Update Staging
  1. Run `fab staging build stop deploy`
  1. Log into the staging mysql db by `ssh staging_admin` and then `mysql -u ycharts -A -per45qw -h staging-2017-06-09.cy4wtovspprb.us-east-1.rds.amazonaws.com ycharts`
  1. Run `delete from django_migrations;`
  1. Run `python /sites/ycharts/manage.py migrate --fake`
  1. Run `fab staging start`

#### 3. Notify developers to update their local branches
1. Send an email/slack message out to all developers with the following instructions:
```
#Update your local develop branch
cd /sites/ycharts
git checkout develop
git pull origin develop

# Delete migrations table
mysql -u ycharts -pycharts -A ycharts
# In mysql shell
delete from django_migrations;
# Exit mysql shell

python /sites/ycharts/manage.py migrate --fake
```
#### 4. Update Production
1. Let staging run for a day to make sure everything looks good and nothing breaks. In the mean time there is a code freeze. No one is allowed to push to `master` or `develop`.
1. On Production (after it runs on staging for at least 1 day):
  1. Prepare a [full release](https://github.com/ycharts/ycharts/wiki/Deployment-Procedures#prepare-release-to-production) to production
  1. Run `fab production build stop deploy`
  1. Log into the production mysql db by `ssh production_admin` and then `mysql -u ycharts -A -per45qw -h production-2017-02-04.cy4wtovspprb.us-east-1.rds.amazonaws.com ycharts`
  1. Run `delete from django_migrations;`
  1. Run `python /sites/ycharts/manage.py migrate --fake`
  1. Run `fab production start`