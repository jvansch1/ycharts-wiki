# Overview
In order to ensure key functions of our application work before releasing code from staging to production, we go through the following user behaviors on our staging environment. All the following actions should happen on [staging.ycharts.com](https://staging.ycharts.com).

## Trial Signup
1. Go to https://staging.ycharts.com/
1. Click "Start 7 Day Trial"
1. Enter user data for sign up. Use a fake email since you will not need to use the actual email address.
1. Get activation key from emails sent to developer-emails@ycharts.com.
1. Activate trial account.
1. You should land on the Dashboard

## Dashboard
1. Ensure Dashboard Charts were created successfully.
1. Go through the "Dashboard Setup".
1. Create a watchlist.
1. Add watchlist to dashboard.
1. View all the tabs of a watchlist on the dashboard.
1. Remove watchlist from dashboard.
1. Reorder watchlist on dashboard.
1. Reorder charts on dashboard.
1. Create new dashboard.
1. Rename dashboard.

## Alerts
1. Create an alert for a stock via the dashboard.
1. Create an alert for a watchlist via the dashboard.
1. Create an alert for investment strategies.

## Equity Screener / Fund Screener
1. Create a basic screen. 
1. Save and open saved screens.
1. Create an alert on a saved screen.
1. Add a metric column to a screen.
1. Reset screen to default state.

## Fundamental Chart / Technical Chart
1. Create a basic fundamental chart
1. Save and open a saved fundamental chart.
1. View chart in presentation mode.
1. Add various options to chart.
1. Export chart data.
1. Save chart as image.
1. Print chart.
1. Reset chart to default state.

## Timeseries Tables
1. Create a basic timeseries table with at least one of each security type.
1. Add security list to table.
1. Add info columns to table.
1. Reorder indo columns.
1. Add metric set to table.
1. Change Date Range.
1. Change Data formatting
1. Change Summary Stats.
1. Test Pagination.
1. Export Data.
1. Reset Data.

## Comparables Tables
1. Create basic comparables tables with at least one of each security type.
1. Add security list to table.
1. Add metric to table.
1. Add info columns to table.
1. Re-order columns of table.
1. Add metric set to table.
1. Add scoring model to table.
1. Export data to CSV.
1. Export data to excel.
1. Reset Data.

## Custom Email Reports
1. go to [CER list view](https://staging.ycharts.com/email_reports/list/)
1. navigate to /new page via "Create New"
    - Fill out `Title`, `Description`, and `Periodicity`
    - add `StockSecuritylistReviewModule`
    - add `WeeklyMarketCommentaryModule`
    - add `FundSecuritylistReviewModule`
    - add `SecurityReturnSummaryModule`
    - add `EconCalendarModule`
1. hit `Save`. Confirm redirect to /edit page.
1. reorder modules. Hit save. Refresh page to confirm ordering is preserved.
1. Preview an individual module via the `Preview` icon. 
1. Preview the entire `CustomEmailReport` via the `"Preview"` button.
1. navigate back to /list.
1. Sort by `"Recently Modified First"`, `"Least Recently Edited First"`, `"A - Z"` and `"Z - A"`
1. `Subscribe` & `Unsubscribe` to a report.
1. `Duplicate` the report.
1. `Delete` the duplicated report.
1. navigate to [CER admin](https://staging.ycharts.com/admin/email_reports/customemailreport/)
1. find most recently created report, navigate to /change page and in the `Tools` tab, hit "Send Report"
1. go to your inbox, and confirm you received the CER. Make sure the modules show up correct.
    - expect the `WeeklyMarketCommentaryModule` to be missing (only added on production) 

## Company Pages

## Index Pages

## Mutual Fund Pages

## Indicator Pages