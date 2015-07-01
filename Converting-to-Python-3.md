# To Run Python 3 Benchmarks

### Install python3
```bash
# Install python3
brew update
brew install python3

# Create new virtualenv
deactivate
mkvirtualenv -p /usr/local/bin/python3 ycharts3

# Test that it's correct
python --version
# Python 3.4.3

# Comment out django-static-sitemaps and createsend from requirements_2.txt
# Change scikits.statsmodels==0.3.1 to statsmodels==0.6.1
# Change mixpanel-py==4.0.2 to mixpanel-py3==3.0.4
pip install -r confs/server/requirements_1.txt
pip install -r confs/server/requirements_2.txt
```

### Handle Some Logic Changes
1. Comment out `'static_sitemaps'` in `INSTALLED_APPS`
1. Change all methods in `apps/email_lists/utils/campaignmonitor.py` to no-ops and comment out `createsend` imports
1. Change `TRACKER` to `None` in `apps/userstats/models.py` and comment out `fasttrack` imports
1. Change `mixpanel_client` to simply `Mixpanel(settings.MIXPANEL_API_TOKEN)` in `apps/tracking/utils/tracking_utils.py` and comment out `mixpanel_async` imports
1. Change all methods in `apps/sales_support/utils/zendesk_utils.py` to no-ops and comment out `zendesk` imports
    - Replace `ZendeskError` with `Exception` in `apps/accounts/tasks.py` and `apps/sales_support/management/commands/sales_support_sync_zendesk.py` and comment out imports
1. Comment out all uses of `cache_utils`
    ```bash
    sed -i '.bak' -e 's/@cached/# @cached/g' **/*.py
    ```
1. Comment out all uses of `honeypot`
    ```bash
    sed -i '.bak' -e 's/@check_honeypot/# @check_honeypot/g' **/*.py
    ```
1. Make some changes to `apps/screener/utils/screener_utils.py`
    1. Around line 113:
        ```python
        # Change this:
        next_set = set([str(i) for i in securitylists.pop()])
        # To this:
        next_set = securitylists.pop()
        ```

    1. Around line 303:
        ```python
        # Change this:
        securitylist_ids = securitylist_ids[start:end]
        # To this:
        securitylist_ids = [i.decode('utf-8') for i in securitylist_ids[start:end]]
        ```
1. Make some changes to `apps/dashboard/utils/dashboard_utils.py`
    1. Around line 98:
        ```python
        # Change this
        watchlist_data = sorted(watchlist_data, key=lambda x: x.get(sort_by), reverse=reverse)
        # To this:
        watchlist_data = sorted(watchlist_data, key=lambda x: x.get(sort_by, 0), reverse=reverse)
        ```
1. Change how `JSONField` gets its metaclass
    ```python
    # Instead of this:
    class JSONField(models.TextField):
        __metaclass__ = models.SubfieldBase
        ...

    # Do this:
    class JSONField(models.TextField, metaclass=models.SubfieldBase):
        ...
    ```

### Base Python 3 Changes
#### Replace urllib2 and urlparse
```bash
# urllib2
sed -i '.bak' -e 's/import urllib2/import urllib/g' **/*.py
sed -i '.bak' -e 's/urllib2.urlopen/urllib.request.urlopen/g' **/*.py
sed -i '.bak' -e 's/urllib2.Request/urllib.request.Request/g' **/*.py
sed -i '.bak' -e 's/urllib2.HTTPError/urllib.error.HTTPError/g' -e 's/urllib2.URLError/urllib.error.URLError/g' **/*.py
sed -i '.bak' -e 's/urllib2.quote/urllib.parse.quote/g' **/*.py
sed -i '.bak' -e 's/urllib2.build_opener/urllib.request.build_opener/g' -e 's/urllib2.HTTPCookieProcessor/urllib.request.HTTPCookieProcessor/g' **/*.py
sed -i '.bak' -e 's/from urllib import urlencode/from urllib.parse import urlencode/g' -e 's/from urllib import unquote/from urllib.parse import unquote/g' **/*.py
sed -i '.bak' -e 's/urllib.urlencode/urllib.parse.urlencode/g' -e 's/urllib.unquote/urllib.parse.unquote/g' **/*.py
sed -i '.bak' -e 's/urllib.urlopen/urllib.request.urlopen/g' **/*.py

# urlparse
sed -i '.bak' -e 's/from urlparse/from urllib.parse/g' **/*.py
sed -i '.bak' -e 's/^import urlparse$/import urllib.parse/g' **/*.py
sed -i '.bak' -e 's/urlparse.urljoin/urllib.parse.urljoin/g' -e 's/urlparse.urlparse/urllib.parse.urlparse/g' -e 's/urlparse.parse_qs/urllib.parse.parse_qs/g' **/*.py
```

#### Relative Imports
```bash
# string_utils
sed -i '.bak' -e 's/^import string_utils$/from apps.main.utils import string_utils/g' **/*.py
```

#### 2to3 Fixes
```bash
# except Exception, e:
2to3 -w -f except -f dict -f imports -f has_key -f long -f basestring /sites/ycharts
```

### Django Python 3 Changes
#### Replace force_unicode
```bash
sed -i '.bak' -e 's/force_unicode/force_text/g' **/*.py
```

#### Miscellaneous
```bash
# Handle ur'unicode raw string'
# Sed sucks so manually edit out the "u" in wikipedia.py
```
