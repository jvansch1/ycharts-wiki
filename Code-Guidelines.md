### Overview

This doc details the common YCharts practices for creating code so everybody can easily read and locate it.  Follow the suggestions and we'll all be happier long term (and you'll spend less time rewriting and moving old code.

# Absolute Basics

Get very familiar with the following principles. They are general practices for good coding:

* [**Don't Repeat Yourself (DRY)**](http://en.wikipedia.org/wiki/Don't_repeat_yourself): Key line from the Wikipedia entry: "Every piece of knowledge must have a single, unambiguous, authoritative representation within a system."
* **Code is read more frequently than it is written** - therefore, work hard to write readable code.  Generally, DRY takes precedence here, but you should try for both.
With that in mind, stick to [PEP 8](http://www.python.org/dev/peps/pep-0008/) guidelines for readability.  If you are bad at remembering lists, install the SublimeLinter linter in Sublime Text (see our [Getting Started With Sublime](https://github.com/ycharts/ycharts/blob/develop/docs/developer_notes/sublime_getting_started.md) doc on github).

NOTE TO ARA: Perhaps we just link to this article - http://www.artima.com/weblogs/viewpost.jsp?thread=331531 - and refer back to it when there are issues?

# Where Things Should Be Located

### If a model/module could conceivably fit into two different apps, how do we choose the proper one?

*Example: I'm creating a calculation executor that grabs data from financials/models.py and creates simple derivatives of those line items (eg. calculating revenue growth rates). We have an app called calculations, where most calculations are run and saved.  Where should I create the executor and save the calculations - in the financials app or the calculations app?*

### How do we determine whether a function goes into a utils file versus remaining a model/class method

*Example: Our function `get_bulk_calc_data(**kwargs)` is a function that grabs data for multiple calculations for multiple companies.  It's a classmethod of of the Company class.  Why is it not a general function in a module like apps/companies/utils/company_utils.py?*

### When should a function go into a file in our "main" app?

*Example: in apps/main/utils/data_utils.py, we have a function
`build_frequency_map(chart_data, max_points)` which maps chart data to different frequencies for display.  Why is that in main instead of in apps/charts/utils/fund_chart_utils.py?*

### When is a project big enough to warrant a new app?

# Dealing With Model Methods

### When should a method carry an @property decorator?

### When should a method be decorated with @memoized or @memoized_property?

### When should a method be decorated with @static_method?

# Static Variables
The general questions here are: "When is it legal to hard-code variables, and how should we hard-code intelligently?"

### When should we use a dictionary (eg. calc_info.py dicts) vs. a database table (e.g. apps.store.models.Feature) for mostly static variables?

