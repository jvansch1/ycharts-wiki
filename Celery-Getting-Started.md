## Getting Started with Celery

### Part 1: Create The Task
Create a tasks.py file in apps.my_app_name
(Look at apps.alerts.tasks or apps.calculations.tasks as an example of completed tasks.py files).

Put your function in apps.my_app_name.tasks.py and add the `@task` decorator.  I usually use the `ignore_result=True` setting.  This is not necessary but saves a bit of time and space if your task function doesn't return any values.  NOTE: Any function with an `@task` decorator in tasks.py will be automatically registered with celery.

### Part 2: Route the task to the proper queue
Go to ycharts.py in our settings folder.

Near Line 59, you will find `CELERY_ROUTES`, the dictionary that routes tasks to the proper queue.

There are three possible queues:

```text
DEFAULT_QUEUE_NAME (known simply as 'main' when choosing a queue using celery worker -Q)
ALERT_QUEUE_NAME (known as 'alerts' when choosing a queue using celery worker -Q)
LATEST_QUEUE_NAME (known as 'latestcalcs' when choosing a queue using celery worker -Q)
```

If timing isn't too important, you don't need to add routing details.  Your task will automatically be routed to the default 'main' queue.  If you're looking to add to another queue (or to create a new one), check with Ara or Pinger.

### Part 3: Create Tools to Queue the Tasks
Create a management command that calls the task, or add the task to

Syntax for your function (assuming the name of your function is function_name and it's been registered properly with celery using the task decorator)

```python
## The .delay() method (shown below) will queue the task and allow it to run asynchronously
>>> function_name.delay(*args, **kwargs)

## Calling the function without .delay() will immediately run the task just like a
## non-celery function
>>> function_name(*args, **kwargs)

# The final method is creating a group of subtasks, which can be executed
# using apply_async(queue='QUEUE_NAME') here is how it looks:
# (Note that groups are routed to our 'main' queue by default.
# You must specifically state if you want a group routed to a different queue.)
from celery import group
tasklist = [function_name.subtask((arguments))] # two sets of parenthesis are necessary!
job = group(tasklist)
job.apply_async(queue='QUEUE_NAME')

# Queue names are contained in ...ycharts/settings/ycharts.py
```


## Part 4: Test

To start up the local celery processing system, run:

```bash
celery -A ycharts worker -E -Q latestcalcs,main,alerts -l info

# Note that you can remove any of the three queues (latestcalcs,celery,alerts)
# and celery will only consume from the remaining queue(s)
```

Then in the shell call your:

```python
>>> function_name.delay(*args, **kwargs)
```

or run your management command.  This will queue tasks to and your celery worker process will begin to consume them.
