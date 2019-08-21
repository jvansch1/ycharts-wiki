# Technology Stack
The following is a description of the major components of our technology stack.

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

## Some Notable JS Libraries We Use
* [Angular.js](http://angularjs.org/) is how all our JS apps are built. We use it for building front-end widgets and controls, for building single-page stateful apps, etc.
* [Highcharts](https://www.highcharts.com/docs/) is the core library we use to create our charts.

## Some Notable NodeJS Libraries We Use
* [Node Canvas](https://github.com/LearnBoost/node-canvas) helps us render our canvas-based charts in Node JS, add title, notes, etc and later save them as PNG

# Getting Started With Development

## Setting up a development environment
The initial setup of a working development environment can be tricky. Talk to any developers with questions, but start with our [dev setup docs](https://github.com/ycharts/ycharts/wiki/Developer-Environment-Setup).