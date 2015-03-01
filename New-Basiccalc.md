## Creating a New Calc in Basiccalc.py

## Checklist of files where calc must be present

### apps/calculations/calc_info.py
* basic_calc_info dictionary - all calc info must be filled in properly (see the documentation at the top of the page for details)
One of the following menus:
* `price_and_valuation_menu_structure`
* `ratios_menu_structure`
* Lite and Free calcs should also be in `company_popular_calc_menu_calcs`

### apps/calculations/models.py
IMPORTANT: The name of the calculation must match for all models (except BasicCalcLatest, where the suffix '_is_prelim' must be added to the calc name)
* BasicCalc class: `models.DecimalField(max_digits=13, decimal_places=4, null=True)`
* CompanyBasicCalcLatest class `models.BooleanField(default=False)`

## apps/calculations/executor/basiccalc.py
Here is where you define the function that actually runs the calculation. Note that the name of the calculation in the function name and in the result object must match (see example below).

### apps/calculations/migrations
* You should use `python manage.py schemamigration --auto calculations <description>` to create new tables when the models.py fields are defined
* Add that migration to git using `git add .` before commiting

### docs/deployment_notes/x_yz_deployment_notes.txt
Add the following to the most recent notes

    # Pre Deploy
    ...
    # Run migrations for new calculations
    python /sites/ycharts/manage.py migrate calculations
    ...

    # Post Deploy
    ...
    # Queue new basiccalcs
    python /sites/ycharts/manage.py companies_tasks_run --task_type basiccalc
    ...

## Process for Adding Calcs
The process for adding one calculation at a time is a bit different than the process for adding multiple calculations at a time. I'll start with the multiple calculations method, because it works both ways.

### Step 1: Create calc_info dictionary entries

Add all of the key code to the dictionary. Order doesn't matter. Pay special attention to the meanings of the 'type', 'frequency', 'date_attr' and 'feature_needed' entries (see the top of calc_info.py for documentation).

    # In apps/calculations/calc_info.py
    basic_calc_info = {
        ...
        'cash_roa_ttm': {
            "ac_start_score": 55,
            "adjustable": False,
            "can_chart": True,
            "date_attr": "quarter_end",
            "feature_needed": "metric",
            "frequency": "quarterly",
            "glossary_term": "cash_roa_ttm",
            "has_aggregate": True,
            "label": "Cash ROA TTM",
            "menu_label": "CFO to Assets (TTM)",
            "months_data_restriction": 60,
            "period": "ttm",
            "quote_dependency": None,
            "quarters_back": 4,
            "secondary_type": "metric",
            "short_label": "CFO to Assets",
            "source_data": [
                "MornQCFR",
                "MornQBSR"
            ],
            "source_fields": [
                "QCF:operating_cash_flow",
                "QBS:total_assets"
            ],
            "statement": None,
            "type": "percent"
        },
        ...
    }

    # Quick note on ac_start_score: you should talk to Pinger, Shek or Ara about that. It is an
    # initial score that helps us to rank in the metric_autocompleter. Look at
    # 127.0.0.1:8000/admin/calculations/debug/autocompleter to find calculations that are similarly
    # popular to the one you are adding, and give it the same starting score as those. Then, you will
    # have to run the following:

    # Creates JSON files that contain proper scores for all calculations to populate the autocompleter.
    # If you are facing merge conflicts, on the get_metric_autocompleter_dict in init.py
    # have it return an empty list and you will be easily able to overwrite everything
    # with the commands below.
    python manage.py calculations_score_autocompleter

    # Re-populates the autocompleter based on the JSON files created by the above command
    python manage.py autocompleter_init --store --remove --clear_cache --name all_calc
    python manage.py autocompleter_init --store --remove --clear_cache --name chart_calc
    python manage.py autocompleter_init --store --remove --clear_cache --name non_fundamental_calc
    python manage.py autocompleter_init --store --remove --clear_cache --name aggregate_calc

    # Also add the calculation to the proper menu (if you're unsure, talk with Pinger or Jeff Shek)
    # If the calculation is has feature_needed': '',
    # also add to the company_popular_calc_menu_calcs list.
    ratios_menu_structure = [
        ...
        ('Management Effectiveness', [
            ...
            'cash_roa_ttm',
            ...
            ]
        ],
        ...

### Step 2: Generate (or type by hand) all fields for models & run migrations

For single calculations, typing the calc names by hand is easiest. For large groups of calculations, writing a quick script to create the fields is preferable (prevents numerous possible typos).

In apps/calculations/models.py dd calculation to BasicCalc model

    class BasicCalc(Calc):
        ...
        cash_roa_ttm = models.DecimalField(max_digits=13, decimal_places=4, null=True)
        ...

    # Add calculation to CompanyBasicCalcLatest model
    # (Note the '_is_prelim' at the end of the name and the field type)

Add calculation to CompanyBasicCalcLatest model (Note the '_is_prelim' at the end of the name and the field type)

    class CompanyBasicCalcLatest(BasicCalc):
        ...
        cash_roa_ttm_is_prelim = models.BooleanField(default=False)
        ...

Add calculation to BasicCalcRank class (Note the field type)

    class BasicCalcRank(Calc):
        ...
        cash_roa_ttm = models.PositiveIntegerField(null=True)
        ...

After this is done, in the shell run:

    $ python manage.py schemamigration --auto calculations <brief_description_of_migration>

    # Then run
    $ python manage.py migrate calculations

Also add the migration to git with one of the following:

    git add .
    git add apps/calculations/migrations/

### Step 3: Create the calculation in basiccalc.py

Now is the time to actually write the formula for the calculation. Note that 'cash_roa_ttm' is included in the name of the function just after 'execute_' and that the name of the result is `self.result.cash_roa_ttm`.

The name of the calculation must match the name in the calc_info.py dictionary, otherwise there will be errors.

### Step 4: Test for errors

To test, you will have to run the celery calculation engine and then queue the calcs.  Here is the code to do so:

    # First, make sure that the queue is clear
    $ python manage.py main_celery_utils --purge main
    $ python manage.py main_celery_utils --purge latestcalcs

Then, start the calculation engine (celery worker) with logging

    celery -A ycharts worker -Q main,latestcalcs -E -l info

Then - in a different terminal window - queue the calcs
(These commands queue for AA, AAPL, PG, and KO, repsectively)

    python manage.py companies_tasks_run --task_type basiccalclatest --object_ids 2,12,3606,2632
    python manage.py companies_tasks_run --task_type basiccalc --object_ids 2,12,3606,2632

Look at the terminal in which you ran the celery worker command for possible errors. If both of these work properly, rankings will work.

After all the calculations have run correctly, check the chart page and the data page to ensure that:
* Latest calcs ran properly
* Historical calcs ran properly
* Calculations are in the drop-down menu

That should be it! Any issues, check with Pinger or Ara.

