# Intro
The purpose of this document is provide details about the project to replace the current flot based based on ycharts.com 

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

The inputs are as follows
```
xAxis: Required, one of 'securities', 'metrics', 'time'
metrics: Optional, list of 0 or more metrics, default []
securities: Required, list of 1 or more securities. Unlike fundamental charts securities must all be indicators are be NOT indicators. If mixed list, take the first security, and filter out other securities of NOT that type.
startDate: Optional, Date in format of MM/DD/YYYY, default None
endDate: Optional, Date in format of MM/DD/YYYY, default None
numTicks: Optional, integer, default None
xAxisLabelOverride: Optional, list of labels for "categories', must match the actual number of categories.
```

# Data Formation / Logic
```
Data formation
if xAxis = securites, then:
    Must have > 1 security
    AND
        startDate AND endDate
        or
        >= 1 metric

if xAxis = metrics, then:
    Must have > 1 metric
    AND
        startDate and EndDate
        or
        >= 1 security

if xAxis = time, then
    Must have startDate, endDate and startDate < endDate
    AND
        >= 1 metric
        AND
        >= 1 security
        Special case: if securities are all indicators, no metric required!
    Must have
        resampleFrequency = one of RESAMPLE_FREQUENCY_OPTION_MAP (default yearly)
        resampleMethod =  one of RESAMPLE_FUNCTION_OPTIONS (default last)
numTicks is optional and must be number
```