# All the steps needed to add a new data frequency

**In main/static/main/js/date_formatter.js**, add the frequency to getFormatter() and
add an appropriate handling function if necessary.

Copy date_formatter.js to the YCharts Node project


In **main/utils/data_formatting_utils.py**, add the frequency to date_format_by_type() and
add an appropriate handling function if necessary.

In **main/utils/data_utils.py**, add the frequency to downsample_chart_data(), build_frequency_map(), set_frequency_map(), add a new downsampling function if necessary

In **main/util/date_utils.py**, add the frequency to FREQUENCY_SCORES, periods_per_year() and
FREQUENCY_LABELS (only if you want it to appear in export center)


In **indicators/importers.py** add in fill handling for the frequency inside infill_date() and normalize_data()

In **indicators/models.py** search for "daily" and make sure we are handling the new frequency properly


In **apps/queue/executor/correlation.py**, add the frequency to correlate() (NOTE: This might be dead soon!)

Write a onetime script to add new version of IndicatorText for the new frequency.
NOTE: this can be a colossal pain in the ass.
