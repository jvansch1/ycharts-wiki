# Technology Stack
The following is a description of the major components of our technology stack.

## High Level Stack
* [Python](http://python.org) is our backend programming language. We are standardized around Python 2.7, though we will likely switch to Python 3.X as soon as most of our major libraries support it.
* [Django](https://docs.djangoproject.com/en/1.4/) is our backend framework.
* [MySQL](http://dev.mysql.com/doc/) is our main storage database. It powers all of our Django models.
* [Redis](http://redis.io/commands) is the data store that powers our screener, autocompleter, queue, among other things.
* [Node.js](http://nodejs.org/api/) is the server technology we use alongside our main Python/Django stack to power specific tasks that are more suited to JavaScript. Currently, it only powers our [Chart Creator](http://ycharts.com/chart_creator) tool and our one click chart embedding.
* [Nginx](http://nginx.org/) serves our web requests. It handles any rewriting, global redirecting, static file serving then proxies requests off to [uswgi](http://projects.unbit.it/uwsgi/) to execute and return requests from actual Python/Django code.

## Some Notable Python Libraries We Use
* [Celery](http://celeryproject.org/) powers our queued execution engine. 90% of our queued jobs are various time consuming financial calculations. Besides that, a few other things are queued like generating sparklines, finding related companies, and downloading news headlines for newly released economic indicators.
* [Pandas](http://pandas.pydata.org/) is a library for ultra-fast financial timeseries transformations. We use it whenever we need to perform operations on timeseries (like calculating Total Return Price, resampling daily data into weekly, etc)
* [django-autocompleter](https://github.com/ara818/django-autocompleter) is our Redis-backed autocompleter. It ties tightly into Django models and provides super-fast results. It supports a variety of fancy features specific to an autocompleter of financial data like aliasing abbreviations, autocompleting on multiple terms for one object (i.e. Apple or AAPL)
* [django-pipeline](https://github.com/cyberdelia/django-pipeline) bundles, compresses and minifies our JS and CSS for efficient serving on the site.
* [Fabric](http://docs.fabfile.org/en/1.4.1/index.html) is used for our automated deployment process.

## Some Notable JS Libraries We Use
* [Angular.js](http://angularjs.org/) is how all our JS apps are built. We use it for building front-end widgets and controls, for building single-page stateful apps, etc.
* [JQuery](http://jquery.com/) is the core util library we use for AJAX requests, DOM manipulation, CSS manipulation.
* [JQuery UI](http://jqueryui.com/) is what we use for some advanced frontend UI elements like the sliders for the screener, the drag/drop on the dashboard page, etc.
* [Flot](http://www.flotcharts.org/) is the core library we use to create our charts.

## Some Notable Private JS Libraries We Use
* [ychart_fund.js](https://github.com/ycharts/ycharts/blob/develop/apps/charts/static/charts/js/ychart_fund.js) sits on top of Flot and acts as the go between between our data and Flot. It handles specific methods for creating good financial charts like data formatting, latest value labeling, pecularities with current day intraday charting, etc.
* [ychart_tech.js](https://github.com/ycharts/ycharts/blob/develop/apps/charts/static/charts/js/ychart_tech.js) sits on top of Flot and acts as the go between between our technical data and Flot. It knows how to calculate and draw all the pecularities of technical charts.

## Some Notable NodeJS Libraries We Use
* [Node Canvas](https://github.com/LearnBoost/node-canvas) helps us render our canvas-based charts in Node JS, add title, notes, etc and later save them as PNGs
* [Flatiron](http://flatironjs.org/) provides the meat and potatoes functionality of our Node based chart creation service. Stuff like request handling, logging, error handling, etc.

## Some Notable SaaS Tools We Use
* [Github](http://github.com/) is where we store our code.
* [Pivotal Tracker](https://pivotaltracker.com/) is where we manage our development (bugs, enhancements, etc)
* [Zendesk](http://zendesk.com) is how we manage customer service requests.
* [Campaign Monitor](http://www.campaignmonitor.com/) is how we send mass e-mails, primarily the daily e-mail of articles written by YCharts editors
* [Postmark](http://postmarkapp.com/) is how we send transactional e-mails, like post Pro sign-ups DRIP campaigns, cancel and billing notices, etc.
* [Marketo](https://marketo.com) is how we manage inbound marketing leads, outbound marketing emails, etc.
* [Google Analytics](http://google.com/analytics) is how we track traffic on the site.
* [Base](https://getbase.com/) is what we use as a CRM/sales cycle management tool.
* [TravisCI](https://travis-ci.com/) runs our tests automatically for every commit to develop/master and every pull request.



# Server / Deployment Environment

## Host
Our production and staging environments are hosted on Amazon's AWS cloud platform. We are big users of many of their services.

* EC2 is used to host our various servers.
* RDS is used as for managed MySQL DB hosting.
* ELB is used to load balance in front of our web servers and Node servers.
* Cloudfront is used as our CDN for our JS embed widgets.
* S3 is used to store any dynamic images on the sites (i.e. article images) as well as to host our embeddable chart images.
* Route 53 is used for our DNS.

## Machine Types
We have several types of machine that perform different functions within our server architecture.
* **Web** one or more machines behind an ELB load balancer serve as our frontend web and application server. They use Nginx which then routes to uwsgi which executes the Python/Django code and returns a response.
* **Admin** one machine per environment runs periodic scripts using cron and also runs various continuously running scripts that update stock quotes and provide other functions that need to happen 24/7.
* **Indicators Admin** one machine per environment continuously runs scripts to import economic data.
* **Node** one or more machines behind an ELB load balancer serve our NodeJS requests. The only use for this right now is to produce image-based charts using the same core JS chart code we use on our main site.
* **Queue** one or machines per environment execute queued tasks, primarily financial calculations based on data that can be changed or updated throughout the day. Queue machines are autoscaled based on queue length. If the queue increases in size, more machines are spun up to help handle the load.

## Deployment
For a full overview of our deployment setup, check out the [deployment prodedures doc](https://github.com/ycharts/ycharts/wiki/Deployment-Procedures) but here are a few quick tips:

1. Releasing to staging is no big deal. Learn there.
1. Releasing to production is a big deal. Don't do it unless you've had somebody walk you through the process a couple times.

After a major deploy, always check deployment notes. Sometimes migrations and new requirements have been added that must run or your dev environment won't work.



# Getting Started With Development

## Setting up a development environment
The initial setup of a working development environment can be tricky. Talk to any developers with questions, but start with our [dev setup docs](https://github.com/ycharts/ycharts/wiki/Dev-Env-Setup).

## Style
Maintaining a clean and consistent style is important to us. As part of the development environment we have
precommit hooks that enforce style rules in Python and JS.

For Python, we follow [PEP8](http://www.python.org/dev/peps/pep-0008/) with a few exceptions for the particularly onerous rules (like 80 character line breaks).

For JS, we use [JSHint](http://www.jshint.com/) as a guide.

Besides the precommit hook we highly recommend installing plugins for your preferred text editor to do PEP8/JSHint checking as you write code.

## Code Editors
Although you are free to use any editor you prefer, our development has standardized around SublimeText and Vim. We have helpful pointers about plugins to install and shortcuts to know for each. The SublimeText notes are [here](https://github.com/ycharts/ycharts/wiki/Sublime) and the Vim notes are [here](https://github.com/ycharts/ycharts/wiki/Vim-Notes)



# Testing Environment
We currently use 3 different testing packages to cover different parts of the codebase.

1. For Python/Django testing we use Django's standard testing setup, with the addition of django-nose/nose to make for more flexible tests for easy Python object mocking.

    ```python
    # To run the full suite of Django tests, do:
    python manage.py test

    # To run the tests for a single app, do:
    python manage.py test apps.APP_NAME.tests
    ```

1. For AngularJS, we use [karma](http://karma-runner.github.io/0.12/index.html)

    ```python
    # To run karma tests, do:
    cd /sites/ycharts
    ./node_modules/.bin/karma start ./confs/karma/karma-unit.conf.js --browsers Firefox --single-run
    ```

1. For testing the YCharts Node codebase we use mocha.

    ```python
    # To run embeds tests, do:
    cd /sites/ycharts_node
    ./node_modules/.bin/mocha test/embeds.js
    ```



# The YCharts Python Codebase
The following headers all represent folders in our YCharts python Django project. Underneath the header are examples of what the folder contains.

## /apps
Apps house our Django apps. In a typical Django project these app directories will be in the top level project directory, but we put them in the apps directory to help disambiguate them from non-app directories.

### Standard App Files
Each Django app has a series of standard files with different functionality. Not all apps have all these files as
they are added only as needed.

* `admin.py` - defines how models can be viewed and edited in the [Django Admin](http://ycharts.com/admin/])
* `admin_views.py` - defines custom views used in the [Django Admin](http://ycharts.com/admin/]) that relate to the app.
* `app.py` - defines a custom `AppConfig` that can override the app's `verbose_name` (used in the Django Admin) or set up signals.
* `autocompleters.py` - defines autocompleter providers for use with [django-autocompleter](https://github.com/ara818/django-autocompleter)
* `feeds.py` - defines any feeds the app produces (as a part of Django's standard syndication system)
* `forms.py` - defines HTML forms necessary for the app
* `lookups.py` - defines any autocompleter looks used by [django-selectable](https://bitbucket.org/mlavin/django-selectable)
* `models.py` - contains the classes that define database tables and key objects, as well as all model methods, properties, etc.
* `signals.py` - contains any signals associated with the app
* `sitemaps.py` - defines any sitemap files the app produces (as a part of Django's standard sitemap system)
* `tasks.py` - contains any tasks associated with a given app that can run asynchronously through a [celery task](http://celery.readthedocs.org/en/latest/index.html)
* `tests.py` - defines test classes that test the functionality of the app
* `urls.py` - contains regular expressions mapping HTTP requests to specific views
* `views.py` - Acts as the interface between an HTTP request and backend code and returns a response.

### Standard App Directories
* `/management/commands/` - holds all managment commands for the app
* `/migrations` - holds Django migrations
* `/onetime_scripts` - holds all onetime scripts assocated with the app
* `/utils` - holds all utility functions associated with the app
* `/static` - holds and JS/CSS/image files associated with the app. These follow a very particular structure so they can later collected by Django's staticfile system for serving on the site.
* `/templatetags` - holds all custom templatetags associated with the app

### The Apps
Here are our apps:

* `about` - contains images and models relating to our about pages like job openings and staff photos
* `accounts` - model for user profile (1-1 model providing extra fields we need for each user) and preferences (1-1 model providing options the user can set), log in, out, register views
* `alerts` - code related to the various alerts we send, except for the changes in Pro Score components (stored in .../companies/models.py) and portfolio changes (stored in .../portfolios/models.py).
* `api` - provides endpoints of an API used by premium users and our Excel plugin to get data out of the system. Centralized here because it draws diverse data from multiple other apps.
* `articles` - code related to the the article we produce [in our analysis center](http://ycharts.com/analysis).
* `calculations` - models + executors for most calculations are here
* `charts` - views and utilities for getting chart data + several single page apps like our [Fundamental Chart](http://ycharts.com/charts/fundamental_chart/) and [Technical Chart](http://ycharts.com/charts/technical_chart/). This is centralized as charts can draw data from objects in multiple other apps.
* `companies` - all information surrounding a company, including data import management commands, utils for getting company data form external sources, and tons of important model methods for accessing company information.
* `countries` - contains models relating to countries and national holidays (which are used by indicators for scheduling purposes)
* `currencies` - contains models relating to currencies and exchange rates
* `dashboard` - code that powers our [Dashboard](http://ycharts.com/dashboard/). Center for Watchlist management, news and charts.
* `email_lists` - connects our users to our Campaign Monitor email lists.
* `embeds` - Management commands, asset files and utilities used to create our interactive JS embeds.
* `estimates` - data model, import scripts and management commands for downloading and processing Zacks Sales/ Earnings Estimates
* `excel` - contains views for downloading and getting information regarding our [Excel Plugin](http://ycharts.com/excel)
* `exchanges` - contains models with information on an exchange. This information is used by most securities.
* `export_center` - everything we need to power our [Data Tables](http://ycharts.com/export_center/multicompany_comparison)
* `financials` - models defining all quarterly and annual financial statements are contained here. Pinger and Ara Know this best.
* `fund_attributes` - models with fund information for CEFs, ETFs, and mutual funds
* `glossary` - powers [our glossary](http://ycharts.com/glossary)
* `indicators` - contains all code for importing, working with and displaying economic indicator data.
* `indices` - code for dealing with indices (like S&P 500, Dow 30, NASDAQ, etc.) and when applicable their constituents
* `investor_relations` - code for information regarding companies' investor relations info, as well as news and events pages
* `main` - is the catchall space for scripts and commands utilities that aren't tied to any one app. All powers our frontpage. The most important command here is `main_execute_daily_tasks.py`
* `marketing_emails` - handles transactional e-mails that are sent to users post regristration / Pro signup.
* `messaging` - handles the yellow pop up box that can display messages on our site.
* `mutual_funds` - contains all models relating to mutual funds as well as management commands for importing the data
* `news` - handles importing of third party news feeds into our site.
* `partners` - holds a generic model for partners that have some business relationship with YCharts. Currently only for partner subscriptions, which are subscriptions that were not paid for on our site.
* `pdf_reports` - code for generating our company PDF reports.
* `portfolios` - models for and utils to help with our pro portfolio strategies.
* `quotes` - utils for dealing with the returning of stock quote data. Centralized in this app because we can get quotes for an number of different objects.
* `resources` - models and views for the [Resources](http://ycharts.com/resources) section of our site.
* `sales_support` - models for tracking the actions of users and syncing them with Base, our sales software.
* `screener` - code that powers our [Stock Screener](http://ycharts.com/stock_screener).
* `search` - views relating to search on our site.
* `securities` - utils for working with multiple types of securities
* `sharing` - views and utils for Facebook, Twitter, and StockTwits sharing
* `store` - Handles all paid product sign up, upgrade, downgrade, cancel, etc.
* `support` - Power the [support center](http://ycharts.com/support)
* `systems` - Views, models, and management commands for systems related things like the [Important Command Dashboard](http://ycharts.com/systems/review) or [Queue Info](http://ycharts.com/systems/review/queue_info)
* `topics` - DEPRECATED - recently removed from the site; kept because it contains information on holdings of investing pro like Warren Buffett, David Einhorn, etc. (13-f filing info). Due to be replaced by direct 13-F from a data provider
* `tracking` - utils for tracking customer activity via [MixPanel](https://mixpanel.com/)
* `userstats` - utils for keeping track of statistics of users
* `watchlists` - models and utils to deal with user watchlists.

## /binaries
Stores external binary programs that we use. This includes:

* `phantomjs` used for running JS on pages we are trying to scrape
* `wkhtmltox` used for converting HTML pages to PDFs
* `ycharts_pdf_to_html` used for converting PDFs to HTML pages
* `yuicompressor` used for minifying/compressing static files

## /confs
Server and developer environment configuration files.

### /confs Subdirectories
* `/aws` stores userdata files that autoscaled queue machines use
* `/cron` stores cronjob files
* `/database` stores plist files used by the Database Restore Mac Mini
* `/developers` stores configuration files needs for the development environment
* `/fonts` stores fonts that are used on the site and by node
* `/karma` stores karma configuration for javascript tests
* `/maintenance` assets needed when taking the site into maintenance mode.
* `/redis` Redis confs
* `/server` Nginx, UWSGI and package installation confs.
* `/ssl` Files needed to set up SSL for the site.
* `/upstart` Configuration files that define long running commands using Ubuntu's upstart system for running/start/stopping tasks

## /data
This folder is reserved for downloading data files before they are processed. It also stores our server log files.

## /docs
Documentation for the site

### /docs Subdirectories
* `/data_providers` contains documentation for our data providers
* `/deployment_notes` is where each new release's must-do tasks are kept
* `/partners` contains information about some of our embed partners
* `/payments` has information regarding braintree setup

## /scripts
Contains bash scripts which we use for things like database restoration from a dump or updating basic auth on staging.

## /ycharts
This is our Django project-level files. This is where things that don't belong in an app go and includes:

* `admin.py` - defines custom admin site which is used to add custom URLs for custom admin views
* `celery.py` - defines our celery application.
* `urls.py` - defines the base site url configuration.
* `wsgi.py` - is used to run the web server!
* `static/` - is where our static files are put after being versioned and bundled by pipeline before uploading to S3
* `templates/` - is where we store templates that don't belong to any apps like our base templates or admin template overrides

### /ycharts/settings
This is where our Django settings are stored. We split our settings into development, staging, and production, because database and other settings differ depending on environments. `active.py` imports the currently active environment. It is by default set to development as is changed to production/staging as part of the deploy process.

Also good to know:
* `django.py` contains the standard django settings
* `pipeline.py` contains settings needed specifically for django-pipeline
* `vendors.py` contains api keys and passwords for our various vendors (Xignite, Zacks, google analytics, etc.)
* `ycharts.py` contains YCharts specific settings, including celery queues, honeypot, etc.

Other folders have names that describe what is in them. They are worth browsing briefly.

## Top level files
* `.travis.yml` - defines the configuration for TravisCI to be able to run our tests.
* `fabfile.py` - defines our deployment tasks for deploying via Fabric.
* `manage.py` - is the entrypoint to Django's management commands.
* `package.json` - defines the node/npm packages that we use.



# YCharts Python/Django Management Commands
A full list of management commands is available by typing:
```bash
python manage.py
```

To get information about management command with name `<scriptname>` and its options.
```bash
python manage.py <scriptname> --help
```

## celery worker
This runs the celery calculation engine for company, sector and industry calculations; indicator news; portfolio strategies; ratings; yscores; etc. We split them up into 3 categories.

1. Main task queue. This task queue for all non time-sensitive tasks.
1. "Latest" tasks. These are tasks that are run regularly to calculate the latest value of a calculation. Since they are typically run once a day, it is important that they are never buried by a backlog of base tasks so they live in their own queue.
1. "Alert" tasks. These are tasks that trigger alerts in our alerts system. They have their own queue because we want to trigger alerts as soon as possible from the occurence of the triggering event.

To run locally:

```bash
celery -A ycharts worker -E -Q main         # Run all base tasks
celery -A ycharts worker -E -Q alerts       # Run all alerts tasks
celery -A ycharts worker -E -Q latestcalcs  # Run all latest tasks

# Run all tasks with INFO level logging
celery -A ycharts worker -E -Q main,alerts,latestcalcs -l info
```

## Other Commands

Like above, start locally with `python manage.py <scriptname>`, or after logging in to admin with ssh, run there with `sudo service <scriptname> start`

Where `<scriptname>` is one of the following

```bash
# Always running on admin
news_populate_company_news_single
news_populate_company_news_full
news_populate_index_news
news_populate_company_tweets
companies_bats_stock_quote_import
companies_morn_realtime_quote_import
indices_xignite_quote_import
main_mturk_process_hits

# Always running on indicators admin
indicators_data_import --importer_priority one --importer_region other
indicators_data_import --importer_priority two --importer_region other
indicators_data_import --importer_priority three --importer_region other
indicators_data_import --importer_priority four --importer_region other
indicators_data_import --importer_priority one --importer_region north_of_50p
indicators_data_import --importer_priority two --importer_region north_of_50p
indicators_data_import --importer_priority three --importer_region north_of_50p
indicators_data_import --importer_priority four --importer_region north_of_50p
```

Before starting all scripts one-by-one, if none are running, you can start all of them with:

```bash
# On production
fab production:admin start # Starts all scripts
fab production:admin stop  # Stops all scripts

# On staging
fab staging:admin start # Starts all scripts
fab staging:admin stop  # Stops all scripts
```



# The YCharts Node Codebase
Documentation for this coming soon!
