# Indicator Guide

## Importers
### General

The place to start when building an importer is with indicator_import_skeleton.py in the Indicators folder. It's just a template with a bunch of the elements you will need to customize already fleshed out.

#### Checklist
+ Indicator names are all in line with Ycharts conventions
+ Scheduler works. Check by going to /admin/indicators/indicator/?report__id__exact={{report_id}}
+ Data types are accurate and in line with `DATA_TYPE_CHOICES` in models.py
+ internal_name on report is unique and set correctly
+ indicator_category is set appropriately, ask James if uncertain


#### Naming Conventions
Short names are not given to most reports.


### Indicator Source

### Indicator Report
Use `internal_notes` to denote which specific data or tables you used to create the indicator.

### Base Fields
`importer_class`: the name of an importer from importers.py that matches the target data source. Most any sane format is covered so you will rarely have to write a custom one.

`report_scheduler_class`: Similar to `importer_class`. The schedulers are in schedulers.py 

### Internal Data

#### Dates
+ `date_format` is the [strftime](http://strftime.org/) representation of the date format on the scheduling page.
  * For cases where the date is split you may need to provide extra information for the importer to work, though you can still use the normal Vertical importers.
  * If you had the below table you need to define `date_column_one`, `date_column_two`, and `date_format`. 

|Year|Month|SomeData|
| --- | --- | ---|
|2010|Q1|1|
| |Q2|2|
| |Q3|3|
| |Q4|4|
|2011|Q1|5|

  * Things to keep in mind here:
    * `date_format` is going to be `{date_column_one's format}-{date_column_two's format}` since the importer will internally set date_value to `date_value_one + '-' + date_value_two`.
    * You also need to set `infill_dates` to `True` because otherwise it won't be able to find a year for Q2-4.
    * The years can come out of Excel as 'YYYY.0' instead of 'YYYY' and is still like that , so keep that in mind for regexes especially in the SplitDate family of importers since the regexes can be used on the dates before and after they are converted back to 'YYYY'.

#### Scheduler Options
+ HTML Table Schedulers
  * `schedule_url` is the url that has the actual scheduling table on it. In the common event of a search being the url try to arrange it so that only future dates are present and they are in ascending order, with the nearest dates at the top.
  * `schedule_date_format` is the [strftime](http://strftime.org/) representation of the date format on the scheduling page.
  * `schedule_date_column` is the column where dates are found. 0-indexed.
  * `schedule_row_label_column` is the column where the report title is found. 0-indexed.
  * `schedule_row_label_regex` is a regex that will match the report titles for relevant rows in the table. Useful for cases where there are other things on the schedule.
  * `schedule_table_row_css_identifier` is the identifier or path that maps to the rows in the schedule table. If you want to verify that the identifier is working try using .cssselect on a lxml html object of the target page to make sure it gets only and all of the table rows.
