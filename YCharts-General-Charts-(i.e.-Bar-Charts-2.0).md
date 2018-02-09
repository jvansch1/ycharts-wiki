# Intro
The purpose of this document is provide details about the project to replace the current flot based bar charts on ycharts.com 

# Goal
Our goal is to replace the current bar charts, with a new, more flexible and functional charting stack that can:
1) Effectively replace all the current bar charts used on the site.
2) Can do more than bar charts! Line charts, stacked bars, and maybe others!
3) Is fully dynamic such that, some day, we could build a single page app where-in users can create your own custom charts.
4) Has full node/"chart generator" support, such that users can save/share charts like they can with fundamental charts.

# Basics
The body of work we are going to embarking on will culminate in the creation of yc-general-chart angular directive that, much like yc-fundamental-chart or yc-technical-chart, will allow us to create a new kind of chart that is either powered by JSON chart data passed into directly, of via a chart URL that it calls to get it's data.

# The Chart
yc-general-chart is a new kind of chart that is discrete in the x-axis. This means that there is no concept of continuous time on the x-axis.  That is what every yc-general-chart will have in common. Otherwise, two yc-general-charts can look very different. Some will be bar charts, others will be line charts. Some will have years in the x-axis, others will have metrics in the x-axis.

In highcharts, this is done with the option x-axis {type: "category"}.

# Data Flow
Just like fundamental charts, we will have an AJAX view where in you pass params and are given back data that is then passed into yc-general-chart.
In the fundamental chart the flow goes like this
fundamental chart app in browser => fund. chart endpoint => yc-fund-chart => ychart_fund.js => highcharts

In the general chart, it will be the same exept there will be no front-end app, so
general chart endpoint => yc-general-chart => ychart_general.js => highcharts

# Inputs // GeneralChartParams
Just like fundamental chart, the general chart endpoint will call into a GeneralChartParams object that will accept a series of params and clean them up such that we can pass them into another class that will query DB and produce the actual chart data.

The inputs are as follows:

**xAxis**: Required, one of 'securities', 'metrics', 'time'

**metrics**: Optional, list of 0 or more metrics, default []

**securities**: Required, list of 1 or more securities. Unlike fundamental charts securities must all be indicators or all be NOT indicators. If mixed list, take the first security, and filter out other securities of NOT that type.

**startDate**: Optional, Date in format of MM/DD/YYYY, default None

**endDate**: Optional, Date in format of MM/DD/YYYY, default None

**resampleFrequency**: Required if time is element in chart (x or y-axis), one of RESAMPLE_FREQUENCY_OPTION_MAP, default yearly, otherwise set to None

**resampleMethod** Required if x-axis = Required if time is element in chart (x or y-axis), one of RESAMPLE_FUNCTION_OPTIONS, default last, otherwise set to None

**yAxisLabelStyle**: Required, one of [short, long], default short

**xAxisLabelStyle**: Required, one of [short, long], default short

**xAxisLabelOverride**: Optional, list of labels for "categories', must match the actual number of categories.

**xAxisLabelNum**: Optional, integer, default None

# Input Rules
```
if xAxis = securities, then:
    Must have > 1 security
    AND
    if securities are all indicators
        O metrics
        optionally, time params
    If securities are all NOT indicators
        >= 1 metrics, NO time stuff
        OR
        1 metric + time params

if xAxis = metrics, then:
    Must have > 1 metric
    AND
        > 1 security (NO INDICATORS)
        OR
        1 security (NO INDICATORS) + optionally, time params

if xAxis = time, then:
    Must have all time params
    AND
        >= 1 security and >= 1 metric
        Special case: if securities are all indicators, no metrics!!!
```

# Category Labeling
```
if x/yAxisLabelStyle = short
   for securities, label = security_id
   for metrics, label = short_label (from calc_info)
if x/yAxisLabelStyle = long
   for securities, label = security_name
   for metrics, label = medium_label (from calc_info)

For time, short/long is irrelevant!!!
if resampleFrequency = yearly, it's time formatted %Y
if resampleFrequency = monthly, it's time formated %b %Y
if resampleFrequency = daily, it's time formatted %b %d

if xAxis in ['metrics', 'securities'] and xAxisLabelOverride is defined and is a list whose length = length of actual "categories" in x-axis, then we use these as categories instead!!!

if xAxis = time, we respect the xAxisLabelNum parameter
in this case we get the number of categories we see in the data we modulus
by number of ticks. That number - 1 is how many categories we null out

So
12 categories
4 ticks
12 % 4 = 3
3-1 = 2
Null out 2 categories in between

before:
['Jan 2012', 'Feb 2012', 'Mar 2012', 'Apr 2012', 'May 2012', 'Jun 2012', 'July 2012', 'Aug 2012', 'Sep 2012', 'Oct 2012', 'Nov 2012', 'Dec 2012']
after:
['Jan 2012', '', '', Apr 2012', '', '', 'Jul 2012', '', '', 'Oct 2012', '', '']
```

# Examples
Note on all examples:
What is the correct in these examples is the inputs, the data that will be eventually passed to highcharts, and the basic set up of how the chart should look.

What is not correct is the hover over interaction, the legend, and the exact look/feel. These should be made to match the current fundamental charts!

## Single security, multiple metrics, category label override

### Goal
![](https://i.imgur.com/qIsqZux.png)

### Inputs
```
xAxis: metrics
metrics: ['revenues_growth_annual', 'revenues_growth_3y', 'revenues_growth_5y']
securities: [{id: AAPL, include: true}]
startDate: ''
endDate: ''
resampleFrequency: ''
resampleMethod: ''
yAxisLabelStyle: ''
xAxisLabelStyle: ''
xAxisLabelOverride: ['1 Year', '3 Year', '5 Year']
```

### Data &  Output
http://jsfiddle.net/hz0savds/5/






## Single security, multiple metrics, across time, category label override

### Goal
![](https://i.imgur.com/2EDC2z1.png)

### Inputs
```
xAxis:'time'
metrics: ['ytd_total_return', 'ytd_total_return_nav']
securities: [{id: PDI, include: true}]
startDate: '12/1/2013'
endDate: '' # (Note null = latest, just like in fundamental charts)
resampleFrequency: 'yearly'
resampleMethod: 'last'
yAxisLabelStyle: ''
xAxisLabelStyle: ''
xAxisLabelOverride: ['2014, '2015', '2016', '2017', '2018 YTD']
```
### Data &  Output
http://jsfiddle.net/2xm3mwk6/3/




## Treasury yield curve - multiple indicators, line chart

### Goal
![](https://i.imgur.com/Jb5ad0l.png)

### Inputs
```
xAxis:'securities'
metrics: []
securities: [{id: I:1MTR, include: true}, {id: I:3MTRNK, include: true}, {id: I:6MTRNK, include: true}, {id: I:1YTRNK, include: true}, {id: I:3YTR include: true}, {id: I:5YTRNK, include: true}, {id: I:10YTR, include: true}, {id: I:20YTRNK, include: true}, {id: I:30YTR, include: true}]
startDate: ''
endDate: '' # (Note null = latest, just like in fundamental charts)
resampleFrequency: 'yearly'
resampleMethod: 'last'
yAxisLabelStyle: ''
xAxisLabelStyle: ''
xAxisLabelOverride: []
```

### Data &  Output
http://jsfiddle.net/j5ywtL1z/2/






## BLAH BLAH

### Goal
xxx

### Inputs
xxx

### Data &  Output
xxx





## BLAH BLAH

### Goal
xxx

### Inputs
xxx

### Data &  Output
xxx
