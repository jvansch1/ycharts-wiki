# Overview
In order to ensure key functions of our application work before releasing code from staging to production, we go through the following user behaviors on our staging environment. All the following actions should happen on [staging.ycharts.com](https://staging.ycharts.com).

## Systems Import Command Dashboard
1. Check https://staging.ycharts.com/systems/review/important_command_dashboard/
1. Any failures should be checked to make sure there is not a bug that is being introduced.

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
1. Export as CSV
1. Reset screen to default state.

## Fundamental Chart
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
1. Add a security list to table.
1. Add a security to a watchlist.
1. Add a metric set to table.
1. Add a single metric to table.
1. Save the group of metrics as a new metric set.
1. Add info columns to table.
1. Re-order columns of table.
1. Open a different metric set; confirm the new metrics replace the old ones
1. Change the Date Range using the "Date Range" dropdown option
1. Change the Date Range using the "Full Year" dropdown option
1. Change the Data Format properties (e.g. Frequency, Aggregation, Fill Method).
1. Add summary stats.
1. Test pagination
1. Export data to CSV.
1. Export data to Excel.
1. Reset all data.

## Comparables Tables
1. Create a basic comparables table with at least one of each security type.
1. Add a security list to table.
1. Add a security to a watchlist.
1. Add a metric set to table.
1. Add a single metric to table.
1. Save the group of metrics as a new metric set.
1. Add info columns to table.
1. Re-order columns of table.
1. Add scoring model to table.
1. Open a different metric set; confirm the new metrics replace the old ones
1. Export data to CSV.
1. Export data to Excel.
1. Reset all data.

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
1. go to your inbox, and confirm you received the CER. Make sure the all the modules show up correctly, with the right Securitylists.
    - expect the `WeeklyMarketCommentaryModule` to be missing (only added on production) 

## Model Portfolios
1. go to [Model Portfolios list view](https://staging.ycharts.com/model_portfolios/list/)
1. click on the drop down next to any MP in the model portfolio list and click export holdings
1. navigate to /new page via "Create New" and click Upload Data.
1. Drag and drop the csv that you exported in an earlier step and click submit in the modal.
1. hit `Save`. Confirm redirect to /list page.
1. You should see the state for the newly created Model Portfolio in calculating. Click on the portfolio to visit it's quote page.
1. Unless the calculation already completed, you should only be able to see the status bar while it's calculation takes place.
1. Once calculation finishes, go through each tab and make sure each module has data and looks correct.
1. Navigate back to /list.
1. `Duplicate` a model portfolio and verify its data gets calculated successfully.
1. `Delete` the duplicated report.

## PDF Reports
1. Go to a Model Portfolio quote page, click on the "Generate Report" button; confirm your reports are shown in the modal
1. Select a report that has the Cover Page module; confirm your default contact info (if any!) is shown in the fields in the modal
1. Change the various fields to custom strings then generate the report; confirm the appropriate values are shown on the cover page
1. Select a report that has the Cumulative Return/Key Stats module; change the title to a custom string then generate the report; confirm your custom title is shown in the Cumulative Return/Key Stats module

## Company Pages

## Index Pages

## Mutual Fund Pages

## Indicator Pages
