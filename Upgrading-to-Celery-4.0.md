## 4.0 Prep - Implement with/on version 3.1.25

* [**REQUIREMENTS**](http://docs.celeryproject.org/en/latest/whatsnew-4.0.html#requirements) we will have update/install these 

## 4.0 Changes - Implement with 4.0 update

* Update Celery settings using the command-line utility provided: 
`$ celery upgrade settings proj/settings.py --django`

* Set the namespace in celery.py:
`app.config_from_object('django.conf:settings', namespace='CELERY')`

* The CLI should up date all settings with the CELERY_ namespace, but double check each settings variable
[Lowercase settings names](http://docs.celeryproject.org/en/latest/whatsnew-4.0.html#v400-upgrade-settings), also if the CLI does not work, manually update based on the list provided

* [Task base class no longer automatically registers tasks](http://docs.celeryproject.org/en/latest/whatsnew-4.0.html#the-task-base-class-no-longer-automatically-register-tasks), we have a few classes that inherit from base class, use @app.task as designated in link

* [Task arugment checking](http://docs.celeryproject.org/en/latest/whatsnew-4.0.html#v400-typing) Celery now checks arguments you send to tasks by matching to the signature, make sure all tasks arguments match their signature

* [Redis Priorities Reversed](http://docs.celeryproject.org/en/latest/whatsnew-4.0.html#redis-priorities-reversed) 0 is lowest, 9 is highest, inline with AMQP

* [Auto-discover now support app configurations](http://docs.celeryproject.org/en/latest/whatsnew-4.0.html#django-auto-discover-now-supports-django-app-configurations) do not need to pass in INSTALLED_APPS as arguments

### Features removed:

The following features have been removed. N/A marks those we do not incorporate, but always worth double checking each one.

* celery.task.http (N/A)
* app.mail_admins (N/A)
* celery.contrib.batches (N/A)
* Using Django ORM as a broker (N/A)
* threads feature (N/A)
* force_execv (N/A)
* celery.contrib.methods (N/A)

### [Reorganization Deprications](http://docs.celeryproject.org/en/latest/whatsnew-4.0.html#reorganization-deprecations)

* It doesn't look like we use and celery.utils, but grep again just to be sure

### [Scheduled Removals](http://docs.celeryproject.org/en/latest/whatsnew-4.0.html#scheduled-removals)

* celery.worker.job >>> celery.worker.request
* celery.task.trace >>> celery.app.trace (celery.task package being phased out)
* [Magic keyword arguments](http://docs.celeryproject.org/en/latest/whatsnew-4.0.html#magic-keyword-arguments) check for these, not sure if used
* [Removed Settings](http://docs.celeryproject.org/en/latest/whatsnew-4.0.html#removed-settings)
* [Changed to internal API](http://docs.celeryproject.org/en/latest/whatsnew-4.0.html#changes-to-internal-api) grep for these and update (doesn't look like we use any on first look)

## New Features!

### New Task Protocol

* [Highlights](http://docs.celeryproject.org/en/latest/whatsnew-4.0.html#new-protocol-highlights) meta-data fields are now sent as message headers to avoid extra decode/encode step, support for other languages, lots of new helpful fields. We may want to implement some going forward.

* [Full Docs](http://docs.celeryproject.org/en/latest/internals/protocol.html#message-protocol-task-v2)


### Tasks

* [Auto-retry decorator](http://docs.celeryproject.org/en/latest/whatsnew-4.0.html#task-auto-retry-decorator)

* [Remote Task Tracebacks](http://docs.celeryproject.org/en/latest/whatsnew-4.0.html#remote-task-tracebacks) adds trace of the worker

* Task connection errors now handle as kombu.exceptions.OperationalError

* Dedicated thread for consuming results for Gevent/Eventlet

* AsyncResult API now support promises 
`company_task.delay(company).then(on_result_ready)`

* [New Task Router API](http://docs.celeryproject.org/en/latest/whatsnew-4.0.html#new-task-router-api) some new options for defining task routes

* [New Periodic Task API](http://docs.celeryproject.org/en/latest/userguide/periodic-tasks.html#beat-entries) We could potentially swap this for crontab

## [Future Deprecation Timeline](http://docs.celeryproject.org/en/latest/internals/deprecation.html#deprecation-timeline)

## STEPS to update

1. Update requirements: 
 - kombu : 4.0
 - billiard : 3.5

To be continued...





