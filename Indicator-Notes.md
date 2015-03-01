## Creation Utils
### Useful Files
1. [importers.py](https://github.com/ycharts/ycharts/blob/develop/apps/indicators/importers.py) - Contains the importers which use `internal_data` to import the data
2. [schedulers.py](https://github.com/ycharts/ycharts/blob/develop/apps/indicators/schedulers.py) - Contains the schedulers which use `internal_data` to schedule the indicator
3. [models.py](https://github.com/ycharts/ycharts/blob/develop/apps/indicators/models.py) - Contains the `Indicator` model, useful for filling in fields
4. [creation_utils.py](https://github.com/ycharts/ycharts/blob/develop/apps/indicators/utils/creation_utils.py) - Contains the utils which are utilized in creating an indicator, not necessary, but incredibly useful
5. [indicator_import_skeleton.py](https://github.com/ycharts/ycharts/blob/develop/apps/indicators/indicator_import_skeleton.py) - The skeleton import file. Provides a good starting point for any import script.

### Importers
The importers contain all the necessary logic to:

* Download the data files
* Process the data files to find the correct data series, and then process that to find data dates and data values
* Save the indicator with the new data

The docstring of an importer should contain all `internal_data` and whether it is required or optional. There is some necessary `internal_data` for all indicators, for example, `schedule_timezone`, `file_extension`, etc. This can be found on the `IndicatorImporter`.

### Schedulers
The schedulers work much the same way as importers, but instead are tasked with finding the correct date *and time* to attempt to import the indicator again. The docstring again should have the required or option `internal_data`.

### Creation Utils
The creation utils file contains three functions

    def create_indicator(**kwargs):
        """
        -   Essentially, this is a wrapper for Django's get_or_create for Indicator with some standard
            boilerplate we perform after creation
        -   This is useful because often only one or two options change so this will reduce
            the amount of space taken up by this boilerplate
        -   Also, this uses sensible defaults so at a minimum, the following fields need to
            be passed to the function
            -   name
            -   internal_name
            -   importer_class
            -   scheduler_class
            -   report
            -   source
            -   categories
            -   region
        -   This will return the Indicator so it can be further manipulated if desired

        OPTIONS:
        ignore_existing -- boolean -- default: False
            -   this will ignore warnings that an indicator
                already exists with the given internal_name or code
        """

    def create_unique_code(name, internal_name, report_name, adjustment_type, frequency):
        """
        Creates a unique all caps code from certain fields on the indicator
        The standard format is 16 characters long with characters:

        -   1-8     - First 4 and Last 4 Capital Letters from name
        -   9-11    - First 2 and Last 1 Capital Letters from report_name
        -   12      - Code taken from adjustment_type
        -   13      - Code taken from frequency
        -   14-16   - First 3 Characters from SHA256 Hash of internal_name
                      which make code unique
        """

    def create_unique_internal_name(name, report_name, adjustment_type='na', frequency='monthly', unit_shorthand=''):
        """
        - Creates a unique internal_name from certain fields on the indicator
        - Internal names tend to have the same types of strings appended to make them unique
        - This will try to append the following, in order, until it finds a unique internal_name
          or fails
            - frequency if different frequencies from existing indicator
            - adjustment_type if different adjustment_type from existing indicator
            - report initials if different reports from existing indicator
            - unit_shorthand if different units from existing indicator
        """

### Tips and Tricks
#### Import Scripts
The import script skeleton has an empty dictionary called `INDICATORS`. A dictionary is a handy way to add a lot of indicators with little boilerplate code.
The general layout of this dict is usually something along the lines of

    INDICATORS = {
        <indicator_name>: {
            'label': <data_label>,
            'unit': <unit>,
            ...
        },
        ...
    }

This way, you can iterate over `INDICATORS.iteritems()` and have all necessary info for each indicator. It is helpful to make use of the `dict.get` method so that if many indicators have the same unit, but only a handful are different, you can only add `'unit'` to those handful and call `dict.get('unit', <default_unit>)`.

If you need to iterate over indicators in a certain order, it is more useful to use a list or tuple of tuples like

    INDICATORS = [
        (<indicator_name>, {
            'label': <data_label>,
            'unit': <unit>,
            ...
        }),
        ...
    ]

You might need to do this so that the correct indicators get the "base" internal_name when using `create_unique_internal_name`.

OrderedDicts could be used for this, but the easiest way to use them would be to pass the above list or iterable of tuples to the constructor, and then you are just calling `INDICATORS.iteritems()` which returns a list of tuples the same as you passed into it, so kind of pointless.

Don't be afriad to build multiple dicts, list, etc. of different parts of names, labels, etc. when you are able to and then have nested loops and build the necessary parts of the indicator within the loop!

#### Creating/Editing in the Admin
The main problem with this will be creating the unique code. The internal_name is generall not a problem creating by hand, but the code can be difficult, but luckily you have options! You can

* Build the code by hand
    * First 4 and Last 4 Capital letters of the name (don't worry if there are fewer, just 8 max chars based on the name)
    * First 2 and Last 1 Capital letters of the report name
    * Lookup the correct code for the adjustment type
    * Lookup the correct code for the frequency
    * Make up 3 characters for the end of the code
    * Check if it is unique by going to `http://ycharts.com/admin/indicators/indicator/?code=<my_new_code>`
        * If there are any results, change the last three characters until there are none

* Fire up a python shell

        >>> from apps.indicators.utils import creation_utils
        >>> creation_utils.create_unique_code(name, internal_name, report_name, adjustment_type, frequency)

