### All the steps needed to add a new data type

1. In `apps/main/static/main/js/number_formatter.js`, add the type to `getFormatter()` and
   add an appropriate handling function if necessary.

1. Copy `number_formatter.js` to the YCharts Node project.

1. In `apps/main/static/main/js/number_format_reverser.js`, add the type to `getReverser()` and
   add an appropriate handling function if necessary.

1. In `/sites/ycharts/ycharts/utils/data_formatting_utils.py`, add handling for the new types in
   `number_format_by_type()`, adding new functions if necessary.

1. In `apps/indicators/models.py`, add the type to `DATA_TYPE_CHOICES`

1. Write a onetime script to add new version of IndicatorText for the new type.
>  NOTE: This can be a colossal pain in the ass.
