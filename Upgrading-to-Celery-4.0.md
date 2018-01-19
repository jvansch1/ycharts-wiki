## Prepare current config

* [Redis Events not backward compatible](http://docs.celeryproject.org/en/latest/whatsnew-4.0.html#redis-events-not-backward-compatible) 'fanout_patterns' and 'fanout_prefix' are enabled by default in 4.0, so configure 3.1 workers to prepare for the upgrade
```
BROKER_TRANSPORT_OPTIONS = {
    'fanout_patterns': True,
    'fanout_prefix': True,
}
```

## Upgrade to Celery 3.1.25

* Deploy workers first, this version can process messages sent by clients using both 3.1 and 4.0

* Upgrade servers next (admin / web machines)

## Begin preparing for 4.0 upgrade with

* [**REQUIREMENTS**](http://docs.celeryproject.org/en/latest/whatsnew-4.0.html#requirements)

* Update Celery settings using the command-line utility provided: 
`$ celery upgrade settings proj/settings.py --django`

* Set the namespace in celery.py:
`app.config_from_object('django.conf:settings', namespace='CELERY')`

* The CLI should up date all settings with the CELERY_ namespace, but double check each settings variable
[Lowercase settings names](http://docs.celeryproject.org/en/latest/whatsnew-4.0.html#v400-upgrade-settings)

* [Json is now the default serializer](http://docs.celeryproject.org/en/latest/whatsnew-4.0.html#json-is-now-the-default-serializer), if we want to continue using pickle, you must configure it in the app, although some of the added functionality may be beneficial? 
```
task_serializer = 'pickle'
result_serializer = 'pickle'
accept_content = {'pickle'}
```

## 4.0 Changes

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

## New Features!

### New Task Protocol

* [Highlights](http://docs.celeryproject.org/en/latest/whatsnew-4.0.html#new-protocol-highlights) meta-data fields are now sent as message headers to avoid extra decode/encode step, support for other languages, lots of new helpful fields

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







