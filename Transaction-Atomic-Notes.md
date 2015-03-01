## Using django.db.transaction.atomic

In django 1.6, `transaction.commit_on_success`, `transaction.commit_unless_managed`, and other
apis have been deprecated in favor of the new `transaction.atomic` function which can handle
everything.

```python
from django.db import transaction
```

## Using transaction.atomic as a decorator:
If an exception occurs in the `bulk_create`, everything that happened in the function will be
rolled back so the objects won't actually be deleted unless the `bulk_create` call succeeds.
This includes anything that occurred in the calls to `do_some_stuff()` and `do_some_other_stuff()`.

```python
@transaction.atomic
def my_func(bunch_of_objects):

    do_some_stuff()

    # Delete some models
    SomeModel.objects.all().delete()

    # Try to create some new ones
    SomeModel.objects.bulk_create(bunch_of_objects)

    do_some_other_stuff()
```

## Using transaction.atomic as a context manager:
If an exception occurs in the `bulk_create`, everything inside the context manager will be
rolled back so the objects won't actually be deleted unless the `bulk_create` call succeeds.
However, differing from above, anything that happened in `do_some_stuff()` or `do_some_other_stuff()`
will still be committed to the database.

```python
def my_other_func(bunch_of_objects):

    do_some_stuff()

    with transaction.atomic():
        # Delete some models
        SomeModel.objects.all().delete()

        # Try to create some new ones
        SomeModel.objects.bulk_create(bunch_of_objects)

    do_some_other_stuff()
```

## Be VERY careful when using a try...except block inside a transaction.atomic block
> You need to be especially careful with catching `DatabaseError` and its subclasses, such as `IntegrityError`,
> because once they are raised, the transaction is broken, but won't be rolled back so any queries performed
> before it's actually rolled back will raise a `TransactionManagementError`

`transaction.atomic` works by checking if it exited due to an exception or not. If an exception
was raised, it will roll back the transaction, otherwise it will commit the changes. This is mostly
similar to `transaction.commit_on_success` however that was not guaranteed to be atomic and the new
function has some gotchas compared to `commit_on_success`.

### Catching an exception
Sometimes you don't want an exception to be raised and stop everything, but you want the changes
to be rolled back if it occurred. Previously, something like the following was used:

```python
@transaction.atomic
def my_func(bunch_of_objects):

    do_some_stuff()

    # Delete some models
    SomeModel.objects.all().delete()

    # Try to create some new ones
    try:
        read_file_and_create_objects()
    except IOError:
        # Roll back the delete(), but don't raise exception
        logger.error('Something bad happened...')
```

This worked with `transaction.commit_on_success` but won't do what you expect with `transaction.atomic`.
Because the `IOError` is suppressed, `transaction.atomic` will commit the delete
even though the create failed. In this case, you can choose to either not catch the exception here and instead
catch it in the caller of `my_func` or you can move the `transaction.atomic` inside the `try`...`except`

```python
def my_func(bunch_of_objects):

    do_some_stuff()

    try:
        with transaction.atomic():
            # Delete some models
            SomeModel.objects.all().delete()

            # Try to create some new ones
            read_file_and_create_objects()
    except IOError:
        # Roll back the delete(), but don't raise exception
        logger.error('Something bad happened...')
```

### Raising an exception

```python
@transaction.atomic
def my_func(bunch_of_objects):

    do_some_stuff()

    # Delete some models
    SomeModel.objects.all().delete()

    # Try to create some new ones
    try:
        read_file_and_create_objects()
    except IOError:
        # Fall back to creating default set of objects
        create_default_objects()
        raise
```

In this case, because an exception was raised, `transaction.atomic` rolls back the entire function, this means
the default objects won't actually be created.

Essentially, you just need to be careful with what goes inside the atomic block and only catch exceptions
inside it if you are certain you don't want to block to roll back and only raise exceptions inside if they
should cause the entire block to roll back.
