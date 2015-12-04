### python 3.4 and django 1.7.7 notes and issues as of Dec 4, 2015

requirenments and dev_requirenments package changes

mixpanel==4.1.0 (needs to be updated to 4.1.0 to support py3k)
-e git+https://github.com/ycharts/zendesk.git#egg=zendesk (use the master)
-e git+https://github.com/mathiasertl/fabric.git@1.1.2#egg=fabric (fabric does not support py3k so use this fork instead)
-e git+https://github.com/ycharts/django_cache_helper.git#egg=django-cache-helper (use the master instead)
-e git+https://github.com/niklasb/dryscrape.git@b5a6708618ff5f1357d7671c12f7c42edcb6ab0e#egg=dryscrape (dryscrape fucks up when you try to run req file,
so get rid of it?)
pyOpenSsl==0.15.1 (what's wrong with this package in python3.4? any alternative?)

########################################
update all `force_unicode` to `force_text`

force_unicode(s, encoding='utf-8', strings_only=False, errors='strict')¶
Historical name of force_text(). Only available under Python 2.


########################
SYNTAX ISSUES IN wikipedia file line below, possible related to utf8 thing

re.sub(ur'[\xc2-\xf4][\x80-\xbf]+', lambda m: m.group(0).encode('latin1').decode('utf8'),\
profile_text) in wikipedia.py file


#######################
In csv_utils.py file
change `import string_utils` to `from ycharts.utils import string_utils` otherwise import error


##############################
We still have some places where we have to do cString fixes. no cStringIO in py3k so use `from io.StringIO import StringIO`


#############################
`basestring` breaks in `honeypot` make it fully py3k compatible?
Is `honeypot` compatible with Py3k? We use @cache(...) decorator

some import issues with hotshot logging library, so I deleted ('ycharts.middleware.ProfilerMiddleware',) from
MIDDLEWARE_CLASSES = MIDDLEWARE_CLASSES + ('ycharts.middleware.ProfilerMiddleware',)


#########################################
  File "<frozen importlib._bootstrap>", line 321, in _call_with_frames_removed
  File "/sites/ycharts/ycharts/storage.py", line 3, in <module>
    from storages.backends.s3boto import S3BotoStorage
  File "/Users/tigran/.virtualenvs/ycharts3.4/src/django-storages/storages/backends/s3boto.py", line 11, in <module>
    from StringIO import StringIO  # noqa
ImportError: No module named 'StringIO'

Deleted from storages.backends.s3boto import S3BotoStorage in ycharts/storage.py file

Note sure what is going on here, really...

################

SO, AFTER ALL ABOVE CHANGES OUR CODEBASE RUNS ON PYTHON 3.4 with some minor errors.


###########################
LONG TERM DJANG AND PYTHON UPGRADE ROADMAP, MAYBE?

3.4 -> 1.7.7 (current)
3.4 -> 1.8.7 (future)
3.5 -> 1.9 (future)


