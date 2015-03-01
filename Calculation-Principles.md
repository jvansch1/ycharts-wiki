## Common Calculation Issues

### Principles We Follow

### 1) No Data is Better than Wrong Data

Our customers get in deep trouble if they use data that is incorrect in an analysis. If you're not sure if you're doing something correctly, ask somebody who is experienced or don't do it at all.

Some examples of this:
* Interpolating missing data is NOT okay - if we don't have it, don't make something up (filling forward is generally okay)
* Use fillna(0) with care - Never substitute the number 0 when it should be a NoneType (see "None or Zero" note below)
* For TTM data, we must have all four prior periods to run a calculation. Use `pd.rolling_sum(series, window=4, min_periods=4)` While technically, we don't need the min_periods, since by default it is equivalent to window, we set it here just to be explicit also.

### 2) Never "Make Up" a calculation

Every calculation has some economic meaning behind it. For example, Debt / Equity ratios describe how a company finances its operations using Bonds, Notes, and other debt vs. using Common Equity, Preferred Equity, etc. These calculations have established standards. Follow them.

As an example, before we had analyst estimates (and even after), I can't tell you how many times people told us our PEG Ratio was wrong because we didn't have an estimated growth rate. People never knew how to calculate it themselves; they just assumed we were wrong because we didn't match Yahoo! Finance.

### 3) Don't blindly copy Yahoo! Finance (or other providers)

Have to write this given the last example. Yahoo! is generally pretty good, but they get things wrong sometimes too. Even the mighty S&P Compustat makes the following odd decision. They calculate:

    Free Cash Flow = Net Income + Depreciation & Amortization Expense

    # which is far from the two accepted standards

    Free Cash Flow = Cash Flow From Operations - abs(Capital Expenditures) # Most common by far

    Free Cash Flow = EBIT * (1 - Tax Rate) + abs(Depreciation and Amortization) - Net Change in Working Capital - abs(Capital Expenditures) # Academically "correct" way

Just be careful. Talk to experts.

### 4) Don't mix Cash Accounting with Accrual Accounting

Less experienced users will ask for things like "Cash ROE" and "Free Cash Flows as a % of Revenue". It's easy to say that any calculation that will help us sell is a good thing to have, but smart users (the ones we want most due to their pull in financial circles) will immediately recognize the error in logic. They won't like it. So don't do it.

### 5) If you're going to use a sub-calculation multiple times, create a private function

This is more of a coding convention than a calculation convention, but it's much easier to fix issues with calculations when all sub-calculations are defined in the same spot. Look at examples like `_get_enterprise_value()` and `_get_long_term_debt()` in companytask.py

### 6) Use Keywords in Functions More

_get_calculation_for_range is used almost everywhere. However, to keep it easier on the eyes, try to use start_date=self.start_date in the arguments as opposed to just calling it by reference point. This is because _get_calculation_for_range can take so many arguments it can get confusing.


## Average Balance Sheet Items for TTM / Quarterly Calculations

There are many calculations like ROE, ROA, Days Sales Outstanding, etc. that use an average in the denominator. These can be tricky with TTM or Quarterly calculations.

Our [ROE Calc Definition](http://ycharts.com/glossary/terms/return_on_equity) describes well why we use 5 quarters in the denominator for the average when comparing to TTM Net Income.

Quarterly ratios of this nature use beginning of quarter and end of quarter numbers from the balance sheet in the denominator, and the quarterly flow number in the numerator.

## Add more comments to each execute_function

More comments the merrier. There's a lot of thought about tail cases that go in for each function that sometimes gets forgotten.

## None or Zero

Many line items can never mean Zero when they are None. Examples Include:
* Revenues (Income Statement)
* Net Income (Income Statement)
* Total Assets (Balance Sheet)
* Shareholder's Equity (Balance Sheet)
* Cash Flows from Operations (Cash Flows Statement)
* etc.

For these line items, None always means "data is missing", not "the value of the line item is zero".

But there are certain line items that are omitted when the company has None. Examples include:
* Interest Expense (Income Statement)
* Long Term Debt (Balance Sheet)
* Preferred Stock (Balance Sheet)
* Net Issuance of Common Stock (Cash Flows Statement)
* etc.

When these line items are None, we could legitimately think of a company as having zero Interest Expense, Long Term Debt, Preferred Stock, etc. I.e. None and Zero are synonyms. We call these `zero_if_none=True` cases, and we have arguments in many functions that allow us to work with these cases.

However, we should use certain rules when running calculations with this data, and not replace None with Zero willy-nilly. Here are the rules when both the first and the second value are `zero_if_none=True` cases

    Number + Number = Number  # Obviously
    None + Number = Number    # Since the first None can be thought of as 0
    Number + None = Number    # Since the secon None can be thought of as 0
    None + None = None        # The weird case; since both numbers are None, we should call their sum None too

    # Note that in longer equations with all zero_if_none=True cases, as long as one element is a number,
    # The output should be a number

## Checklist of Common Pandas Issues

The following are very common mistakes that people make when creating calculations using series. The list is not exhaustive, but it should catch the majority of common errors

### Reindexing

Mixing daily and quarterly or quarterly and annual numbers is a mess. Always make sure data is of the same frequency before performing operations on it. Otherwise, you will get a whole bunch of NaN values in your results. Try it.

Great pandas tools include:

    # Look them up - they're methods on Series and DataFrame objects
    asfreq()
    reindex_like()
    resample()

Read all of the sections below carefully. They will help a lot with weird reindexing gotchas.

### Always fill forward

Spend a bit of time thinking this through. Filling forward is the same thing as sitting here today and looking back to the previous quarterly report. We can see anything from the past today (as if it were filled forward).  We cannot see anything in the future (as if it were filled backward). So never fill backward.

### Weekend Cases

Often scenarios you will pull price, however be aware that due to quarter_end dates, we frequently have a mismatch of having quarterly information based on a weekend. Therefore, when using price, remember to make it a daily price frequency prior to calculating.

### Beware missing starting data

If you are expecting quarterly data and a quarter is missing, reindexing will fill straight through the missing quarter, even though we should've treated the missing quarter as a None value. Try it, and look at the function `_get_calculation_for_range()` in companytask.py for a good way to handle this issue.

### Understand Closure

This is a hugely important concept, especially when working with data like dividends, issuances of stock, long term debt, etc. that can not exist for a period of time, then exist, then cease to exist according to the whims of a company's management.

Imagine that you have two data Series:

    s1 = Series(...) # Daily frequency, starting 1/1/2000, ending 1/1/2010
    s2 = Series(...) # Monthly frequency, starting 1/1/2001, ending 1/1/2009

We say that s1 is "Closed" with regards to s2

Thus by definition, following code will yield a series s3 that has values for all dates in s2:

    s3 = s1.asfreq('M', method='pad').fillna(0) + s2

    # This works because s1 starts before
    # s2 and ends afterward, (and we have filled NaN values)
    # so we have no worries about filling before or after the last
    # date actually in the series.


We say that s2 is NOT "Closed" with respect to s1

By definition, the following code will yield a whole bunch of NaN data at both the start and the end of the new series s4 (even given the fillna(0) at the end of s2)!

    s4 = s1 + s2.asfreq('D', method='pad').fillna(0)

    # This happens because we have no empty values before 1/1/2001
    # (in s2) that we can use to fill-forward to 1/1/2001. Hence, there is nothing to
    # add to the daily data from s1 between 1/1/2000 and 1/1/2001.

    # Similarly, we have no data in s2 betwen 1/1/2009 and 1/1/2010 because there
    # was no date after 1/1/2009 in s1 to which we can fill forward.

The correct way to deal with the second case (provided that `s2` is a `zero_if_none=True` case) is to run the following:

    s4 = s1 + s2.reindex_like(s1).fillna(0)

    # This will first add extra indices to s1 with NaN values, then fill those
    # NaN values with zero, then add the series, resulting in values for all
    # periods.
