The following is a description of the major components of our technology stack, deployment and development process.

## High Level Stack
* [Python](http://python.org) is our backend programming language. We are standardized around Python 3.x
* [Django](https://docs.djangoproject.com/en/1.4/) is our backend framework.
* [MySQL](http://dev.mysql.com/doc/) is our main storage database. It powers all of our Django models.
* [Redis](http://redis.io/commands) is the data store that powers our screener, autocompleter, queue, among other things.
* [Node.js](http://nodejs.org/api/) is the server technology we use alongside our main Python/Django stack to power specific tasks that are more suited to JavaScript. Currently, it only powers our [Chart Creator](http://ycharts.com/chart_creator) tool and our one click chart embedding.
* [Nginx](http://nginx.org/) serves our web requests. It handles any rewriting, global redirecting, static file serving then proxies requests off to [uswgi](http://projects.unbit.it/uwsgi/) to execute and return requests from actual Python/Django code.

## Some Notable Python Libraries We Use
* [Celery](http://celeryproject.org/) powers our queued execution engine. 90% of our queued jobs are various time consuming financial calculations. Besides that, a few other things are queued like generating sparklines, finding related companies, and downloading news headlines for newly released economic indicators.
* [Pandas](http://pandas.pydata.org/) is a library for ultra-fast financial timeseries transformations. We use it whenever we need to perform operations on timeseries (like calculating Total Return Price, resampling daily data into weekly, etc)
* [django-autocompleter](https://github.com/ara818/django-autocompleter) is our Redis-backed autocompleter. It ties tightly into Django models and provides super-fast results. It supports a variety of fancy features specific to an autocompleter of financial data like aliasing abbreviations, autocompleting on multiple terms for one object (i.e. Apple or AAPL)
* [Airflow](https://airflow.apache.org/) is used to handle dependency management. For YCharts, every day there are a number of data providers from which we get data, and when those imports are done we need to do various calculations on them. Airflow handles the dependency tree of that process such that one action can start as soon as another is done.

## Some Notable JS Libraries We Use
* [Angular.js](http://angularjs.org/) is how all our JS apps are built. We use it for building front-end widgets and controls, for building single-page stateful apps, etc.
* [Node Canvas](https://github.com/LearnBoost/node-canvas) helps us render our canvas-based charts in Node JS, add title, notes, etc and later save them as PNG. It is used in our "ycharts_chart_generator" repo.
* [Highcharts](https://www.highcharts.com/docs/) is the core library we use to create our charts.
* [Webpack](https://webpack.js.org/) is used to bundle our static assets

## Some Notable SaaS Tools We Use
* [Base](https://getbase.com/) is what we use as a CRM/sales cycle management tool.
* [Github](http://github.com/) is where we store our code.
* [Google Analytics](https://google.com/analytics) is how we track traffic on the site.
* [Mixpanel](https://mixpanel.com) does more fine grained user/event tracking so we know how our users use our appication.
* [Pagerduty](https://www.pagerduty.com) manages our on-call schedule. All site errors and issues are funneled here, which then notifies the person(s) on call and then escalates as necessary.
* [Postmark](https://postmarkapp.com/) is how we send transactional e-mails, like cancel and billing notices, etc, password reset verifications and so on.
* [Pivotal Tracker](https://pivotaltracker.com/) is where we manage our development (bugs, enhancements, etc)
* [Hubspot](https://hubspot.com) is what we us for marketing automation. Every new trial on the site is sent there, and then Hubspot manages things like DRIP campaigns and the like. 
* [Sentry](https://sentry.io/) is what tracks our Python/JS errors and exceptions as they occur.
* [TravisCI](https://travis-ci.com/) runs our tests automatically for every commit to develop/master and every pull request.
* [Zendesk](https://zendesk.com) is how we manage customer service / support requests.

# Hosting & Deployment

## Hosting
Our production and staging environments are hosted on Amazon's AWS cloud platform. We are big users of many of their services.

* EC2 is used to host our various servers.
* RDS is used as for managed MySQL DB hosting.
* Elasticache Memcached is used to cache data.
* Elasticache Redis is used as a datastore for various services, from our task queue, to our security lists, to intermediate data used to make our screening function faster.
* Cloudfront is used as our CDN.
* S3 is used to store to host our embeddable generated chart images.
* Route 53 is used for our DNS.

## Deployment
We use what's called an "Immutable Deployment" strategy. The basic idea is we create "artifacts" that represents the code we want to release. Then we deploy these artifacts. The advantages of this setup are that once we have the artifacts created, we can deploy at will w/o depending on any external services like Github or pypi or what not. 

In our case our artifacts are Amazon Machine Images or AMIs. We use [Packer](https://www.packer.io/) to create the AMIs and use [Ansible](https://www.ansible.com/) to deploy them into Amazon "Auto Scale Groups" or ASGs. 

[This document](https://github.com/ycharts/ycharts_systems/wiki/Deployment-Overview) provides an overview of our deployment procedures while [this one](https://github.com/ycharts/ycharts_systems/wiki/Deploy-and-Hotfix) goes through the commands you need to run to do a deploy. 

## Architecture / Topology

[This chart](https://www.lucidchart.com/documents/edit/3ed05ef6-e356-462d-8eda-d255d65ce5bb/0_0) represents our current production network toplogy. [This chart](https://www.lucidchart.com/documents/edit/7546c880-820d-4d0b-9207-8a663fb17c3c/0_0) shows the different AMIs that power each auto scale group. [This chart](https://www.lucidchart.com/documents/edit/5c6fc033-6af6-4d6d-85c4-0348fe0529bc/0_0) goes into more detail as to the exact machine types and network setup.

These are the distinct AMIs that exist:

### ycharts.com
* **Web** one or more identical machines behind a load balancer serve as our frontend web and application server. They use Nginx which then routes to uwsgi which executes the Python/Django code and returns a response.
* **Admin** various distinct machines that run periodic scripts using cron and/or airflow and also runs various continuously running scripts that update stock quotes and provide other functions that need to happen 24/7.
* **Workers** Several groups of machines that each are assigned to handle tasks from distinct task queues. For example, our "Portfolio" worker group handles tasks for model portfolios, our "Latest" worker group the daily tasks that need to run every day to get our data and calculations ready for market open. Worker machines are autoscaled based on queue length. If the queue increases in size, more machines are spun up to help handle the load.

### get.ycharts.com
* **Marketing Web** one or more identical machines that run Wordpress and power our marketing site on get.ycharts.com

### charts.ycharts.com
* **Chart Generator** one or more identical machines that run NodeJS and power our chart generation service that turns dynamic chart URLs into static images and puts them on S3.

### airflow.ycharts.com
* **Airflow Web** one or more identical machines that access a special Airflow database that visually displays the status of our dependency graphs that are run/managed by Airflow

# Development

## Setting up a development environment
The initial setup of a working development environment can be tricky. Talk to any developers with questions, but start with our [dev setup docs](https://github.com/ycharts/ycharts/wiki/Developer-Environment-Setup).

## Style
Maintaining a clean and consistent style is important to us. As part of the development environment we have
precommit hooks that enforce style rules in Python and JS.

For Python, we follow [PEP8](http://www.python.org/dev/peps/pep-0008/) with a few exceptions for the particularly onerous rules (like 80 character line breaks).

For JS, we use [JSHint](http://www.jshint.com/) as a guide.

Besides the precommit hook we highly recommend installing plugins for your preferred text editor to do PEP8/JSHint checking as you write code.

## Code Editors
Although you are free to use any editor you prefer, our development has standardized around [PyCharm](https://github.com/ycharts/ycharts/wiki/PyCharm). We also have some note around using [Vim](https://github.com/ycharts/ycharts/wiki/Vim-Notes).

## Testing Environment
We currently use a few different testing packages to cover different parts of the codebase.

1. For Python/Django testing we use Django's standard testing setup.
    ```python
    # To run the full suite of Django tests, do:
    python manage.py test
    # To run the tests for a single app, do:
    python manage.py test apps.APP_NAME.tests
    ```

1. For JS, we use Jest.

    ```python
    # To run jest tests, do:
    npm run test
    ```

# The YCharts Codebase
95% of our code resides in one monolithic Repo. This repo holds all the Python/Django code for our backend and all the JS/Angular code for our frontend. The only major piece of our service not in this repo is our "Chart Generator" service which is it's own distinct NodeJS based service in its own repo.

The following headers all represent folders in our YCharts python Django project. Underneath the header are examples of what the folder contains.

## /apps
Apps house our Django apps. In a typical Django project these app directories will be in the top level project directory, but we put them in the apps directory to help disambiguate them from non-app directories.

### Standard App Files
Each Django app has a series of standard files with different functionality. Not all apps have all these files as they are added only as needed.

* `admin.py` - defines how models can be viewed and edited in the [Django Admin](http://ycharts.com/admin/])
* `admin_views.py` - defines custom views used in the [Django Admin](http://ycharts.com/admin/]) that relate to the app. Mostly these days they are included in admin.py, but we still have some in admin_views.py
* `apps.py` - defines a custom `AppConfig` that can override the app's `verbose_name` (used in the Django Admin) or set up signals.
* `autocompleters.py` - defines autocompleter providers for use with [django-autocompleter](https://github.com/ara818/django-autocompleter)
* `constants.py` - Contains app-level constants
* `feeds.py` - DEPRECATED defines any feeds the app produces (as a part of Django's standard syndication system)
* `forms.py` - defines HTML forms necessary for the app, as a part of the Django's forms system.
* `lookups.py` - defines any autocompleter looks used by [django-selectable](https://bitbucket.org/mlavin/django-selectable)
* `models.py` - contains the classes that define database tables and key objects, as well as all model methods, properties, etc.
* `signals.py` - contains any signals associated with the app
* `sitemaps.py` - DEPRECATED defines any sitemap files the app produces (as a part of Django's standard sitemap system)
* `tasks.py` - contains any tasks associated with a given app that can run asynchronously through a [celery task](http://celery.readthedocs.org/en/latest/index.html)
* `tests.py` - defines test classes that test the functionality of the app.
* `urls.py` - contains regular expressions mapping HTTP requests to specific views
* `views.py` - Acts as the interface between an HTTP request and backend code and returns a response. This can sometimes we a directory containing different test files.

### Standard App Directories
* `/management/commands/` - holds all managment commands for the app
* `/migrations` - holds Django migrations
* `/onetime_scripts` - holds all onetime scripts associated with the app
* `/services` - contains any service classes associated with the app
* `/static` - holds and JS/CSS/image files associated with the app. These follow a very particular structure so they can later collected by Django's staticfile system for serving on the site.
* `/tests` - For apps that may have lots of tests, we break tests up into different files and put it in this directory.
* `/templates` - store any Django templates associated with the ap
* `/templatetags` - holds all custom templatetags associated with the app
* `/utils` - holds all utility functions associated with the app
* `/views` - For apsp thats have lots of views,  we break views up into different files and put it in this directory.

### The Apps
Here are our apps:
* `accounts` - model that represents users  and preferences (1-1 model providing options the user can set), log in, out, register views
* `alerts` - code related to the various alerts we send, except for the changes in Pro Score components (stored in .../companies/models.py) and portfolio changes (stored in .../portfolios/models.py).
* `api` - provides endpoints of an API used by premium users and our Excel plugin to get data out of the system. Centralized here because it draws diverse data from multiple other apps.
* `articles` - DEPRECATED code related to the the article we produce [in our analysis center](http://ycharts.com/analysis).
* `calculations` - models + executors for most calculations are here
* `charts` - views and utilities for getting chart data + several single page apps like our [Fundamental Chart](http://ycharts.com/charts/fundamental_chart/) and [Technical Chart](http://ycharts.com/charts/technical_chart/). This is centralized as charts can draw data from objects in multiple other apps.
* `classifications` - contains models relating to [sector and industry data](https://ycharts.com/classifications/sectors) (ex. NAICs, MornSector, and MornIndustry).
* `companies` - all information surrounding a company, including data import management commands, utils for getting company data from external sources, and tons of important model methods for accessing company information.
* `custom_pdf_reports` - All the backend generation code and front-end widgets needed to provide users Custom PDF reports based on one or more securities.
* `countries` - contains models relating to countries and national holidays (which are used by stocks/funds for grouping purposes and indicators for scheduling purposes)
* `currencies` - contains models and data importers relating to currencies and exchange rates
* `dashboard` - code that powers our [Dashboard](http://ycharts.com/dashboard/). Center for watchlist management, news and charts.
* `data_templates` - For all the different things that a user can save on the system, like a screen, or a comp table, or a chart, we have templates that we can either push to all users or a library of which they can browse so they can look at interesting views w/o having to figure out how to use our tools right away.
* `embeds` - DEPRECATED Management commands, asset files and utilities used to create our interactive JS embeds. We have not created embeds for quite a while, but still have a few out there on the web
* `estimates` - data model, import scripts and management commands for downloading and processing Revenue/ Earnings estimates from S&P Global
* `events` - Small app that holds a service for gathering what we consider events for different securities. Events can be actual events, or stuff like splits and dividends.
* `excel` - contains views for downloading and getting information regarding our [Excel Plugin](http://ycharts.com/excel)
* `exchanges` - contains models with information on an exchange. This information is used by most securities.
* `financials` - models defining all quarterly and annual financial statements and their corresponding importers  are contained here.
* `fund_attributes` - Funds, be they ETFs that live in the companies app or mutual funds that live it in the mutual_funds app, come with a series of attributes that describe them. These are things like geographic make up of holdings, credit quality of holdings, expense and fees structures and so on. This app contains all the models that store this data as well as all he importers that import this data.
* `funds` - aside from the shared data that's contained in fund_attributes, funds, be they ETFs that live in the companies app or mutual funds that live it in the mutual_funds app, can share other things like templatetags that display certain fund-specific information. This app contains that shared code that can be leveraged in the apps like companies or mutual_funds where the views actually live.
* `glossary` - powers [our glossary](http://ycharts.com/glossary)
* `home` - contains the HTML/CSS/images, etc needed to display our logged out [homepage](https://ycharts.com)
* `indicators` - contains all code for importing, working with and displaying economic indicator data.
* `indices` - code for importing, working with and displaying indices (like S&P 500, Dow 30, NASDAQ, etc.) and when applicable their constituents.
* `investment_firms` - Investment firms have to report their holdings of stocks. This app contains code to import that data from an upstream provider (WhaleWisdom) and store it.
* `mutual_funds` - contains all models relating to mutual funds as well as management commands for importing the data
* `news` - handles importing of third party news and press releases feeds into our site.
* `notes` - holds user-entered notes on a company, index, or fund.
* `partners` - holds a generic model for partners that have some business relationship with YCharts. Currently only for partner subscriptions, which are subscriptions that were not directly billed via our billing partner, but are either free or are invoiced separately.
* `pdf_reports` - DEPRECATED before we had the custom_pdf_reports app, we had this app that generated one-pagers or stocks and funds.
* `portfolios` - DEPRECATED in 2016-2017 we tried to bring in client data via an aggregator named "By All Accounts" or BAA. This app contains the models and importers for that effort. It never really took off and is not being developed.
* `quickflows` - an app that contains a service that makes it seamless to "flow" from one page/app to another and see useful, contextual views.
* `quotes` - A tiny app used for dealing with the returning of stock quote data. Centralized in this app because we can get quotes for an number of different security types
* `sales_support` - models for tracking the actions of users and syncing them with Base, our sales software.
* `screener` - code that powers our [Stock Screener](https://ycharts.com/screener/stock/) and our [Fund Screener](https://ycharts.com/screener/mutual_fund_and_etf/)
* `search` - A centralized place for search related views, components and utils. This powers our main site autocompleter as well as the search page itself.
* `securities` - A centralized place to share templatetags, utils, services, mixins, etc that are shared across multiple security types.
* `security_lists` - We have a concept of lists of securities that can mix and match various security types. This app contains all code related to generating, storing and accessing those lists.
* `separate_accounts` - Stores models and importers needed to import data for "Separate Accounts" (SA) or "Separately Managed Acccount" (SMA) securities.
* `sharing` - views, models and utils for internal sharing of items (SavedScreens, MetricSets, etc.) with users in the same ClientGroup.
* `store` - Handles all paid product sign up, upgrade, downgrade, cancel, etc.
* `strategies` - Stores our [investment strategies](https://ycharts.com/strategies)
* `support` - Power the [support center](http://ycharts.com/support)
* `systems` - Views, models, and management commands for systems related things like the [Important Command Dashboard](http://ycharts.com/systems/review) or [Queue Info](http://ycharts.com/systems/review/queue_info)
* `tables` - code that powers our [Data Tables](https://ycharts.com/tables/comparison/) and our [Timeseries Analysis](https://ycharts.com/tables/timeseries/)
* `tracking` - utils for tracking customer activity via [MixPanel](https://mixpanel.com/)
* `userstats` - utils for keeping track of statistics of users
* `watchlists` - models and utils to deal with user watchlists.

## /binaries
DEPRECATED - This used to store various compiled binaries that we used. Right now it only stores `yuicompressor` which is used to compress JS/CSS in our legacy frontend packaging system.

## /confs
Server and developer environment configuration files.

### /confs Subdirectories
* `/airflow` store airflow specific confs
* `/celery` store celery-specific confs
* `/cron` stores cronjob files
* `/database` stores plist files used by the Database Restore Mac Mini
* `/developers` stores configuration files needs for the development environment
* `/fonts` stores fonts that are used on the site and by node
* `/incident` ssets needed when taking the site into incident mode.
* `/maintenance` assets needed when taking the site into maintenance mode.
* `/redis` Redis confs
* `/server` Nginx confs for our webservers and related assets.
* `/systems` Configuration files that define long running commands using Ubuntu's systemd service for running/start/stopping tasks

## /data
This folder is reserved for downloading data files before they are processed. It also stores our server log files.

## /docs
Documentation for the site

### /docs Subdirectories
* `/data_providers` contains documentation for our data providers
* `/deployment_notes` is where each new release's must-do tasks are kept
* `/partners` contains information about some of our embed partners
* `/payments` has information regarding braintree setup

## /media
This empty directory is where our static assets end up when they are compiled and bundled.

## /scripts
Contains a random collection scripts which we use for things like database restoration from a dump or updating basic auth on staging or running chromeheadless

## /wheelhouse
This empty directory is where our python packages end up when they are collected as a part of the build process. 

## /ycharts
This is our Django project-level files. This is where things that don't belong in an app go and includes:

### /ycharts Subdirectories
* `/dags` - This is where we keep the directed a-cyclical graphs used by Airflow to do our data pipeline.
* `/executor` - Celery task execution base classes.
* `/fixtures` - Data fixtures used for testings
* `/importers` - Base classes used by our various data importing classes.
* `/settings` - This is where our Django settings are stored. We split our settings into development, staging, and production, because database and other settings differ depending on environments. `active.py` imports the currently active environment. It is by default set to development as is changed to production/staging as part of the deploy process. Additionally, the sensitive settings files are encrypted using git-secret! Anything like a password or key used to log in to another service like an upstream data provider is stored in secrets.py
* `static/` - Project level static files like JS/CSS/Images that don't belong in any apps.
* `templatetags/` - Project level templatetags that don't belong in any app
* `templates/` - Project level templates that don't belong in any app
* `tests/` - Project level tests that don't belong in any app
* `utils/` - Project level util functions and classes that don't belong in any app

### /ycharts files
We are not going to go through every file in /ycharts, as many of them mirror similar standard files in each app, but there are a few worth noting:
* `admin.py` - Defines custom admin site which is used to add custom URLs for custom admin views
* `celery.py` -Defines our celery application.
* `constants.py` - Project level constants
* `context_processors.py` - This is context that is inserted in each and every context that's rendered into Django templates.
* `decorators.py` - This stores some decorators we used throughout the site like for example memoization decorators.
* `export.py` - Some base classes we use to export data
* `fields.py` - Defines some custom DB fields that are used in various models.
* `middleware.py` - Defines code that we might want to attach to a request to intercept and do something against. For example, if we want to profile each request for a while.
* `widgets.py` - Custom widgets used to render some of our custom fields.
* `wsgi.py` - is used to run the web server!

## Top level files
We are not going to go through every top level file, just a few worth noting:
* `.babelrc` - Defines Babel config that drives our transpilation from ES6 to code that works in the browser we are trying to target.
* `.flake8` - We use flake8 to check that our Python code is clean, this is the config for that.
* `.travis.yml` - defines the configuration for TravisCI to be able to run our tests.
* `dev_requirements.txt` - defines the python packages needed to develop on YCharts, in addition to the packages needed in requirements.txt.
* `jest.config.js` - We use Jest to run some JS tests. This is the config for that.
* `manage.py` - is the entrypoint to Django's management commands.
* `package.json` - defines the node/npm packages that we use. This is mostly for our front-end code and packaging there of.
* `requirements.txt` - defines the python packages and versions our Python code depends on being installed.
* `webpack.config.js` - defines the configuration of Webpack which handles our front-end code packaging, bundling, compressing.

