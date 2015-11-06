### Contents

Alert Models
* Alert Examples
* Setting Alerts

Tasks

* Main Asynchronous Functions
* Sending Emails
* Helper Functions

Miscellaneous Supporting Tools
* Company raw deltas (used to generate all non-limit company alerts)
* Portfolio raw deltas (used to generate all portfolio alerts)
* Cleanup tasks
* How alerts are run by our system

# Detailed App Functions and Interactions


## AlertBase

Excluding alerts set on watchlists, all alerts are created through the AlertBase class method `create_alert_for_type(security_type, alert_type, alert_data)`.  Using the security type and alert type, we get athe appropriate model from ALERT_TYPE_TO_CLASS and call its own _create_alert(alert_data) method.

## AlertWatchlist

Alerts set on watchlists are set by creating an AlertWatchlist which is tied to a specific watchlist.  AlertWatchlist creates the individual alerts (in a similar fashion to AlertBase) for each watchlist item.  When a watchlist is updated (by adding or removing items), alerts/signals.py handles creating and deleting corresponding alerts.

A security AlertWatchlist (ex. AlertCompanyWatchlist) is a proxy model on AlertWatchlist (similar to how Watchlist works).

# Types of alerts (alerts.models)

Alerts are organized first by security_type (ex. companies, funds, indicators, portfolios) and then by the alert_type (calc limit, portfolio entry/exit, news, events, etc.).

## Alert Examples

### class AlertCompanyCalcLimit(AlertCompanyBase, AlertCalcLimit)

Allows users to set an alert on any calculation for any company. For example: Alert me when the price of AAPL falls to $550.00.

### class AlertIndexCalcLimit(AlertIndexBase, AlertCalcLimit)

Allows users to set an alert on any calculation for any index. For example: Alert me when the level of ^DJI falls to 17770.

### class AlertMutualFundCalcLimit(AlertMutualFundBase, AlertCalcLimit)

Allows users to set an alert on any calculation for any index. For example: Alert me when the price of M:VFIAX rises above 200.

### class AlertIndicatorLimit(AlertIndicatorBase)

Allows users to set an alert on any calculation for any index. For example: Alert me when the value of I:USUR falls below 5%.

AlertIndicator is similar in function to to other Limit alerts except that it does not operate on a calculation.  Instead, the limit_name attribute determines what data we pull fron the Indicator.  The values that we currently accept are stored in INDICATOR_FIELD_NAME_TO_LABEL and comprises of "last_value", "change_year_ago_percent", and "last_change_percent".  This is why AlertIndicatorLimit looks like a copy of AlertCalcLimit but does not inherit from it.

### class AlertCompanyPortfolio(AlertCompanyBase)

Alerts when a chosen stock enters/exits Portfolio Strategies. Eg. Alert me when MSFT enters or exits any Portfolio Strategy.

### class AlertCompanyRating(AlertCompanyBase)

Alerts users when a company's overall rating changes.  Eg. Alert me whenever MSFT's overall rating changes.

### class AlertCompanyValueScore(AlertCompanyBase)

Alerts when a chosen stock's value score changes.

### class AlertCompanyFundamentalScore(AlertCompanyBase)

Alerts when a chosen stock's fundamental score changes.

### class AlertPortfolio(AlertBase)

Allows users to "follow" Portfolio Strategies.  Eg. Alert me whenever new stocks enter or exit the Large Cap Value Strategy. 

The preceding alert models all inherit security information from an Alert Security class while inheriting (when possible) alert specific information from an Alert Type class.

We also make alerts unique to the security and to a user to prevent duplicate alerts.

## Fund alerts

As in other places, funds are of note because they live in both the Company and MutualFund models.  In this case, we use the securit_typey: alert_type structure in order to create different Alert objects but keep it tied to say, the same FundWatchlist.  Also because of this distinction, CalcLimits for FundWatchlists will silently fail on creation for a fund that doesn't have the calculation.  For example, Net Income is a company only calculation.  When creating an alert on a fund watchlist for Net Income, MutualFunds will be ignored.


## Class Variables

### ALERT_TYPES

This variable is worth looking at.  It contains useful info about various types of alerts, and contains the standard names for each alert.

    ALERT_TYPE_TO_CLASS = {
	    'company':{
	        'calc_limit': AlertCompanyCalcLimit,
	        'overall_rating': AlertCompanyRating,
	        'fundamental_score': AlertCompanyFundamentalScore,
	        'value_score': AlertCompanyValueScore,
	        'portfolio': AlertCompanyPortfolio,
	        'financial_data': AlertCompanyFinancialData,
	        'news_data': AlertCompanyNewsData,
	        'events': AlertCompanyEventData,
	    },
	    'index':{
	        'calc_limit': AlertIndexCalcLimit,
	        'news_data': AlertIndexNewsData,
	    },
	    'indicator':{
	        'indicator_limit': AlertIndicatorLimit,
	    },
	    'mutual_fund':{
	        'calc_limit': AlertMutualFundCalcLimit
	    },
	    'portfolio':{
	        'portfolio': AlertPortfolio
	    }
	}

### EMAIL_FREQUENCIES
Currently there are three types of email frequencies:

    EMAIL_FREQUENCIES = (
        ('a', 'As Soon as Possible'), # this will be intraday for price-based calcs and end of day for all others
        ('ah', 'After Hours'), # This executes alerts that run after US market hours
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

 __Main Asynchronous Functions__

### execute_alerts()

No arguments - this function runs alerts based on a frequency set when the AlertExecutor is initialized.
This is the real workhorse function.  It executes all alerts for users who fit the current frequency.  This is probably the **best central starting point for learning about alerts.**  It operates by first iterating through all of the alert models to find the users that have potentiall actionable alerts.  Then we execute each user's actionable
 alerts.

__Helper functions__

### _get_actionable_alerts(security_type, alert_type, send_email=False):
Gets all of the alert items we have to consider during this cycle of alerts execution for a specific alert model.  We use security_type and alert_type to determine which model (using ALERT_TYPE_TO_CLASS).  Whether the alert is actionable is determined by email preferences and fre
quency.

### _send_summarized_alert_email(self, user, alerts_by_type, test_only=False):

This function summarizes all alerts into one email for the user.  It takes two arguments.

`alerts_by_type` is a dictionary of alert types that have been triggered and will be sent to the user.
Alert types are named in apps.alerts.models.ALERT_TYPE_TO_CLASS

The HTML template used is stored in 'alerts/emails/basic_alert.html'.  The actual sending of the email is handled through user_email_utils.

# Miscellaneous Supporting Tools

### apps.companies.models (Company raw delta objects)
* class CompanyRatingDelta(models.Model)
* class CompanyValueScoreDelta(models.Model)
* class CompanyFundamentalScoreDelta(models.Model)

### apps.portfolios.models (Portfolio strategy raw delta objects)
* class CompanyPortfolioDelta(models.Model)

### Cleanup tasks
* apps.companies.tasks.clean_company_deltas(date) - cleans all CompanyRatingDelta, CompanyValueScoreDelta and CompanyFundamentalScoreDelta objects
* apps.portfolios.tasks.clean_portfolio_deltas(date) - cleans all old CompanyPortfolioDelta object

Both of these are run in apps.systems.management.commands.management_command.systems_execute_daily_tasks

# How alerts are run by our system

`apps.systems.management.commands.management_command.systems_execute_daily_tasks`
Runs all daily and weekly alerts.  It also removes stale deltas from apps.companies.models tables and apps.portfolios.models tables (see above).

`apps.alerts.management.commands.alerts_queue_tasks`
Runs our intraday alerts and after hours alerts.