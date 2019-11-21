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
  1. Change into systems directory `cd /sites/ycharts_systems`
  1. Run `ycharts_deploy staging --manual_migration`
  1. When the deploy gets to the manual migration, it will pause.
  1. SSH into the migrations admin by opening a new terminal and within in the `ycharts_systems` directory run `ycharts_ssh staging migrations_admin`
  1. Within the staging `migrations_admin` machine log into the staging database by running `mysql -u ycharts -A -p[STAGING_DATABASE_PASSWORD] -h [STAGING_DATABASE_HOST] ycharts`
  1. Within the mysql shell run: ```delete from django_migrations;```
  1. Then exit the mysql shell via: ```exit;```
  1. Then on the `migrations_admin` machine run: `python /sites/ycharts/manage.py migrate --fake`
  1. Once the above command has finished we are good to go. Go back to the terminal window that was paused and hit enter so the deploy finishes.

#### 3. Notify developers to update their local branches
1. Send an email/slack message out to all developers with the following instructions:
```
# Update your local develop branch
cd /sites/ycharts
git checkout develop
git pull origin develop

# On your mac, not vagrant, run the following
# 1. Login to your mysql shell
mysql -u ycharts -pycharts -A ycharts
# 2. Delete the django_migrations table.
mysql> delete from django_migrations;
# 3. Exit mysql shell
mysql> exit;

# 4. SSH into vagrant.
cd /sites/ycharts && vagrant ssh
# 5. Fake migrations in local DB.
python /sites/ycharts/manage.py migrate --fake
```
2. All Developers that have open PRs with migrations need to re-do their migrations by:
    - Deleting migrations that are in the PR.
    - Running `python manage.py makemigrations`

#### 4. Update Production
1. Let staging run for a day to make sure everything looks good and nothing breaks. In the mean time there is a code freeze. No one is allowed to push to `master` or `develop`.
   > Reason no one is allowed to commit to develop (master is obvious) is because in the case that something does go wrong, we need to be able to roll back that commit.
1. On Production (after it runs on staging for at least 1 day, preferably 2):
  1. Prepare a [full deploy](https://github.com/ycharts/ycharts_systems/wiki/Deploy-and-Hotfix-%5BYCharts%5D) to production
  1. Run `ycharts_deploy production --manual_migration`
  1. Log into the production mysql db by `ycharts_ssh production migrations_admin` and then `mysql -u ycharts -A -p[PRODUCTION_DB_PASSWORD] -h [PRODUCTION_DB_HOST] ycharts`
  1. In mysql shell run: `delete from django_migrations;`
  1. Run `python /sites/ycharts/manage.py migrate --fake`
  1. Let the deploy finish by hitting enter.
