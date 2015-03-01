### Contents

Alert Models

Tasks

* Main Asynchronous Functions
* Helper Functions

Alert Utils
* Setting and Deleting Alerts
* Getting Deltas for Alert Generation

Sending Emails

Miscellaneous Supporting Tools
* Company raw deltas (used to generate all non-limit company alerts)
* Portfolio raw deltas (used to generate all portfolio alerts)
* Cleanup tasks
* How alerts are run by our system

# Detailed App Functions and Interactions

## Types of alerts (alerts.models)

###class UserAlert(models.Model)

Abstract class that contains common elements between alerts.  Attributes include:

    user = models.ForeignKey(User) # The user who set the alert and who will be sent the alert
    create_date = models.DateField(auto_now_add=True) # The date the alert was created
    modify_date = models.DateField(auto_now=True) # Date the alert was last modified
    is_triggered = models.BooleanField(default=False) # Once the alert is triggered, this is set to true
    trigger_date = models.DateTimeField(null=True, blank=True) # Date the alert is triggered
    is_emailed = models.BooleanField(default=False) # True if the alert has been emailed
    email_date = models.DateField(null=True, blank=True) # Date the alert was emailed
    feature_needed = models.CharField(max_length=100, default='') # Feature need to use the alert

### class CompanyCalcLimitUserAlert(UserAlert)

Allows users to set an alert on any calculation for any company.  For example: Alert me when the price of AAPL falls to $550.00.

    company = models.ForeignKey(Company) # The company to which the alert applies
    calculation = models.CharField(... choices=calc_info.get_calculation_menu_named_list(menu_type='all', include_pro_calcs=True))
    # calculation is the name of the calculation that the user wants to be alerted about.
    limit_type = models.CharField(max_length=3, choices=LIMIT_TYPES) # 'gte' (Greater than or equal to) or 'lte' (less than or equal to)
    limit_value = models.DecimalField(max_digits=13, decimal_places=2) # the value at which the alert should trigger

### class CompanyPortfolioAlert(UserAlert)

Alerts when a chosen stock enters/exits Portfolio Strategies. Eg. Alert me when MSFT enters or exits any Portfolio Strategy.

### class CompanyRatingAlert(UserAlert)

Alerts users when a company's overall rating changes.  Eg. Alert me whenever MSFT's overall rating changes.

### class CompanyValueScoreAlert(UserAlert)

Alerts when a chosen stock's value score changes.

### class CompanyFundamentalScoreAlert(UserAlert)

Alerts when a chosen stock's fundamental score changes.

The four preceding alert models above all have the same attributes:

    company = models.ForeignKey(Company) # The company that the user wants to follow
    last_checked = models.DateTimeField() # The last time we checked to see if there was motion for the company

All of these alerts are unique to a company for a specific user to prevent duplicate alerts.

### class PortfolioUserAlert(UserAlert)

Allows users to "follow" Portfolio Strategies.  Eg. Alert me whenever new stocks enter or exit the Large Cap Value Strategy.

    portfolio = models.ForeignKey(Portfolio) # The portfolio that the user is following
    last_checked = models.DateTimeField() # The last time we checked for changes in the portfolio.

__Class Variables__

### EMAIL_FREQUENCIES
Currently there are three types of email frequencies:

    EMAIL_FREQUENCIES = (
        ('a', 'As Soon as Possible'), # this will be intraday for price-based calcs and end of day for all others
        ('d', 'Daily'), # using the end of day close price for all price-based calcs
        ('w', 'Weekly'), # these will be run every Saturday Morning
    )

Daily and weekly alerts should still be triggered intraday for display on the homepage, but they will only be emailed according to the frequency chosen by the user.

### LIMIT_TYPES

There are only two operators that we use for limit types, which can be combined and matched to create basically any odd alert you want

    LIMIT_TYPES = (
        ('gte', 'Greater Than or Equal To'),
        ('lte', 'Less Than or Equal To'),

    )

## alerts.tasks

 __Main Asyncronous Functions__

### execute_asap_alerts()

No arguments - this function runs alerts that should be triggered as soon as possible

### execute_daily_alerts()

No arguments - runs alerts executeded daily

### execute_weekly_alerts()

No arguments - Runs alerts executed weekly

### execute_all_user_alerts(user_id, time)

This is the real workhorse function.  It executes all alerts for a given user.  This is probably the **best central starting point for learning about alerts.**

__Helper functions__

### execute_limit_alert(alert, email_now)

Takes an alert object called `alert` and a boolean variable `email_now`.  Returns a message and the alert object to create an email or post to homepage.
Also modifies the alert so it is triggered, sends an email if necessary, marks that an email has been sent, and creates a message for the homepage if necessary.

    # Given the following:

    alert1.company.symbol = 'AAPL'
    alert1.limit_type = 'gte'
    alert1.calculation = 'price'
    alert1.limit_value = Decimal('500.00')
    # Current price of AAPL is 600.00

    >> message, alert = execute_limit_alert(alert1, True)
    # Email will be sent to the user and message, alert will have the following values
    >> alert
    <CompanyCalcLimitUserAlert: CompanyCalcLimitUserAlert object>
    >> message
    u'AAPL <a href="http://ycharts.com/companies/AAPL/price">Price</a> rose to your target level of 500.00. Its current value is 600.00'

Messages for other alerts will be similar.

### execute_portfolio_alert(alert)

Checks to see if the portfolio in `alert.portfolio` has had new entrants or exits.  Returns a message and the alert as `execute_limit_alert()` does.

### execute_comp_port_alert(alert)

Checks a company `alert.company` to see if the company has entered or exited any portfolios.  Returns a message and the alert as `execute_limit_alert()` does.

__Bulk Alerts__

These alerts all check all alerts of the same type between the dates `last_checked_date` and `datetime.date.today()` for all companies in `company_list` (a list of ids).
They return a list of messasges similar to `execute_limit_alert()`, but not a single alert.

### execute_overall_rating_alerts(user, last_checked_date, company_list)
### execute_value_score_alerts(user, last_checked_date, company_list)
### execute_fundamental_score_alerts(user, last_checked_date, company_list)

## alerts.utils.alert_utils

### ALERT_TYPES (variable)

This variable is worth looking at.  It contains useful info about various types of alerts, and contains the standard names for each alert, similar to `calc_info` in `apps.calcs`.

    ALERT_TYPES = {
        'company_limit': {
            'model': CompanyCalcLimitUserAlert,
            'global': False, # 'global' tells whether an alert is global (contstantly being checked for all companies) or not
            'company': True, # 'company' tells if an alert is related to a specific company
        },
        'overall_rating': {
            'model': CompanyRatingAlert,
            'global': True,
            'company': True,
        },
        'fundamental_score': {
            'model': CompanyFundamentalScoreAlert,
            'global': True,
            'company': True,
        },
        'value_score': {
            'model': CompanyValueScoreAlert,
            'global': True,
            'company': True,
        },
        'company_portfolio': {
            'model': CompanyPortfolioAlert,
            'global': True,
            'company': True,
        },
        'portfolio': {
            'model': PortfolioUserAlert,
            'global': True,
            'company': False,
        },
    }

__Setting and Deleting Alerts__

### global_alert_for_watchlist(alert_type, watchlist, email_frequency)

Sets the same alert type for all stocks in the watchlist.

`alert_type` is any string in `alert_utils.ALERT_TYPES.keys()` where `ALERT_TYPES[alert_type]['gobal'] == True`

`watchlist` is any `CompanyWatchlist` object.

`email_frequency` is any value in `apps.alerts.models.EMAIL_FREQUENCIES`

For example:

    >> global_alert_for_watchlist(alert_type='overall_rating', watchlist=mywatchlist, email_frequency='a')
    # Sets an overall rating alert for all companies in mywatchlist to be checked As Soon as Possible'

### limit_alert_for_watchlist(watchlist, calculation, limit_type, limit_value, email_frequency)

Does the same thing as global_alert_for_watchlist() shown above, but takes 3 different arguments:
1. `calculation` is the internal name of any calculation in our system
2. `limit_type` is the standard 'gte' or 'lte' for a limit alert
3. `limit_value` is the value at which the alert should be triggered

### delete_all_user_alerts(user)

Does exactly what it sounds like it should do for a given `django.contrib.auth.User` object `user`

__Getting Deltas for Alert Generation__

### get_portfolio_company_deltas(portfolio, start_date, end_date)

This function will get all changes to a portfolio between `start_date` and `end_date` (both of which are datetime objects).  `portfolio` is a Portfolio class object.

    # Import necessary items
    >>> from apps.portfolios.models import Portfolio
    >>> from apps.alerts.utils import alert_utils
    >>> import datetime

    # Get a portfolio
    >>> lcv = Portfolio.objects.get(internal_name='large_cap_value')

    # Get entrants and exits
    >>> alert_utils.get_portfolio_company_deltas(lcv, datetime.date(2012,5,3), datetime.date(2012,5,7))
    {'entrants': [u'BLK', u'LMT', u'MET', u'MRO', u'PEG', u'PPL', u'WAG', u'WFC', u'WLP', u'LYB'], 'exits': [u'DUK', u'ETN', u'EXC', u'FE', u'NOC', u'PRU']}
    # This is all the changes in the portfolio between 5/3/2012 and 5/7/2012

### get_company_portfolio_deltas(company, start_date, end_date)

Takes a `Company` class object and two datetimes, `start_date` and `end_date`.  Returns all portfolios the company entered or exited over the given timeframe.

    # Import necessary items
    >>> from apps.companies.models import Company
    >>> from apps.alerts.utils import alert_utils
    >>> import datetime

    # Get a company
    >>> wag = Company.objects.get(symbol='WAG')

    # Get portfolios the company entered and exited.
    >>> alert_utils.get_company_portfolio_deltas(wag, datetime.date(2012, 5, 3), datetime.date(2012, 5, 7))
    {'entered': [{'portfolio_name': u'Large Cap Value', 'internal_name': u'large_cap_value'}], 'exited': []}

### get_company_deltas(delta_type, company_id_list, start_date, end_date)

Takes four arguments:
1. `delta_type` can be 'overall_rating', 'value_score' or 'fundamental_score'
2. `company_id_list` a list of company ids, eg. [1, 2, 12] for Agilent, Alcoa and Apple
3. `start_date` a datetime.date() object showing the day before the range to look at starts
4. `end_date` a datetime.date() object showing the day the range to look at ends.

Samples:

    # Import alert_utils
    >>> from apps.alerts.utils import alert_utils

    # Get overall rating deltas
    >>> alert_utils.get_company_deltas('overall_rating', [3075], datetime.date(2012,5,1), datetime.date(2012,5,3))
    {u'MSFT': {'new_rating': None, 'old_rating': u'1-attractive'}} # MSFT's company id is 3075

    # Get value score deltas
    >>> alert_utils.get_company_deltas('value_score', [3075], datetime.date(2012,5,1), datetime.date(2012,5,3))
    {u'MSFT': {'new_rating': 9L, 'old_rating': None}} # MSFT's company id is 3075

## alerts.utils

__Sending emails__
### send_alert_email(alerts, template, subject, recipient, send_notice=True)

This funciton will rarely change, but here are the arguments:

`alerts` a list of tuples of the form (alert, message) or (user, message), depending on the alert type.

`template` the html template 'alerts/emails/basic_alert.html'

`subject` changes depending on the type of alert sent (see send_summarized_alert_email())

`recipient` is the email address to which the email will be sent (string type variable)

### send_summarized_alert_email(alert_data, alert_types)

This function summarizes all alerts into one email for the user.  It takes two arguments.

`alert_data` a list of tuples of the form (alert, message) or (user, message), depending on the alert type.

`alert_types` is a list of alert types that have been triggered and will be sent to the user.
Alert types are named in apps.alerts.utils.alert_utils.ALERT_TYPES.keys()

## Miscellanious Supporting Tools

### apps.companies.models (Company raw delta objects)
* class CompanyRatingDelta(models.Model)
* class CompanyValueScoreDelta(models.Model)
* class CompanyFundamentalScoreDelta(models.Model)

### apps.portfolios.models (Portfolio strategy raw delta objects)
* class CompanyPortfolioDelta(models.Model)

### Cleanup tasks
* apps.companies.tasks.clean_company_deltas(date) - cleans all CompanyRatingDelta, CompanyValueScoreDelta and CompanyFundamentalScoreDelta objects
* apps.portfolios.tasks.clean_portfolio_deltas(date) - cleans all old CompanyPortfolioDelta object

Both of these are run in apps.main.management.commands.main_execute_daily_tasks

### How alerts are run by our system

`apps.main.management.commands.main_execute_daily_tasks`
Runs all daily and weekly alerts.  It also removes stale deltas from apps.companies.models tables and apps.portfolios.models tables (see above).

`apps.alerts.management.commands.alerts_queue_tasks`
Runs our intraday alerts.
