## How To Add Info Columns:

## Where Are They?
- Screener (screener.html)
- Multisecurity Comparison (Export Center) (multisecurity_comparison.html)
- Multisecurity Timeseries (Export Center) (multisecurity_timeseries.html)

## Making Columns Work

### Front End
In each location, add it to appData.  This may be consolidated and moved to a single provider at some point. cssClass will, most importantly, dictate the width of the column it will be put in.

	    AppDataProvider.setData('infoColumnData', [
        	{label: 'Name', value: 'name', cssClass: "colName"},
        	{label: 'Currency', value: 'currency_code', cssClass: "colCurrency"}
        ]

### Backend
We currently have a file: apps.companies.utils.info_column_utils.py.  Simply add the column value (eg: pro_rating) and label to the two collections.

	# This array just keeps track of all of the fields on a company that are to be used.  Should reflect Core Manager, except @property decorated model methods are acceptable.
	BASIC_INFO_FIELDS = ['id', 'currency_code', 'name', 'symbol', 'hq_country_display'...]

	# This contains the val label mapping which is used for CSV export writing
	INFO_COLUMN_HEADER_MAP = {'name': 'Name', 'currency_code': 'Currency',...}


### Screener Sorting:
Will need to create an asc and desc list in redis in the all companies task.  Key should be on the value sent by the option.
eg: all_companies.current_fiscal_year_end_day_display.asc.

## Other Things to Do
### Add to company profile
See profile.html in company templates.  Appears in sidebar of company pages

### Add to serializers
Ask an excel expert if you have any questions.  Most items require only to be added to the list of fields.
