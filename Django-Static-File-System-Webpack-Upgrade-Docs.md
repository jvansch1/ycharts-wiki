### Django Static File System
This document reviews django's `staticfiles` system, how it works in our apps, and how `django-pipeline` does its jobs. The step-by-step outline then will allow us to plug in Webpack to fulfill the same functionalities.

#### ABC of Django's Static File System
Reference: [Official Docs](https://docs.djangoproject.com/en/2.0/ref/contrib/staticfiles/)


**1 Configure**
- `STATIC_ROOT` specifies the **destination** to `collect` all static file to, which in our case is
 the `media` folder.
- `STATIC_URL` is the base url from which to retrieve static assets from the frontend.
- `STATICFILES_DIRS` specifies, in addition to default static paths, where django should find 
static files to be collected.
- `STATICFILES_FINDERS` dictates how files are collected from `static directories` into 
`STATIC_ROOT`. This defaults to the built-in `FileSystemFinder` and `AppDirectoriesFinder`.


**2 Serving in Development**
- `django.contrib.staticfiles` injects a `StaticFilesHandler` into the `runserver` command, so 
when `runserver` is run with `Debug` set to `True`, this handler is called to serve all the 
static files in local development environment.
    - [Source Code](https://github.com/django/django/blob/master/django/contrib/staticfiles/management/commands/runserver.py)
    - This step **ONLY** servers `staticfiles`, but does **NOT** `collect`static files. i.e. it 
    expectes everything to be in the `STATIC_ROOT` directory at this poiint


**3 Using the Static Files**

Using django directly
```html
{% load static %}
<img src="{% static "my_app/example.jpg" %}" alt="My image"/>
```

Similarly, with `django-pipeline`
```html
{% load pipeline %}
<!--These below are in fact script/link tags-->
{% javascript 'group' %}
{% stylesheet 'group' %}
```


**4 Deployment**

Typical Steps
1. run `collectstatic`
2. move the static files to a static file server to be served. This depends on the 
`STATICFILES_STORAGE`, which implements a `post_process` method to do all sorts of magics.

YChart's current deployment steps:
1. (least relevant) collect sass/css separately
2. replace angular template urls with actual inline templates
3. run `collectstatic`/`post_process` with a customized storage (see 'What does 
`django-pipeline` do?' blelow)
4. create a `JSONP` copy of the manifest 


#### What does `django-pipeline` do?
1 Copy static files to `STATIC_ROOT` on `runserver` by patching the `static view` with its 
`collector`
- [Source Code](https://github.com/jazzband/django-pipeline/blob/master/pipeline/views.py)
- This step would delete outdated files as necessary, etc, similar to `collectstatic`
- Note that this step does **NOT** run compiler, etc.
- What does this do? Django itself, though not collecting the files, will 'pull' files in from `static` folder (at least with the help with `django-pipeline`). However, if collect is disabled on `pipeline`, then all the assets specified by wildcards would not be found and made available.

2 Rendering - some magic here
- [Source Code](https://github.com/jazzband/django-pipeline/blob/master/pipeline/templatetags/pipeline.py)
- The templatetag, during its rendering process, will call pipeline's `packager`, which does the
compiling/compression magic.

3 `post_process` magics
- For this one, we use a customized `STATICFILES_STORAGE`, which is the mixin of `PipelineMixin` and 
django's built-in `ManifestStaticFilesStorage`.
- The [`PipelineMixin`](https://github.com/jazzband/django-pipeline/blob/master/pipeline/storage.py) 
implements the main logic, i.e. the magical ` post_process`. This is actually simply packaging, e.g. 
running compiler, compression, etc. Plus of course other mixin/base storage's `post_process` command, 
e.g. manifest.
 