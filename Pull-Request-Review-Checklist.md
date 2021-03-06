# Pull Request Review Checklist #

## Screener ##

### Security Universe Filters
1. Add a Filter
    - Does the popup look correct?
    - When I add, does the result set shrink correctly (CHECK COUNTS!)
    - Check add/intersect/exclude
2. Edit a Filter
    - Similar to add.
    - Does the popup open populated correctly?
3. Remove A Filter
4. Check the "remove duplicates" box
    - Do results decrease?
5. Check y-ratings options
    - Results should be pretty clear about whether it's working or not.  Only have attractive selected?  Should only see green in the results.

### Metric Filters
1. Add a Metric Filter
    - Test all types: Between, less than, greater than etc.
    - Test the formula entry, calc selection, range data etc.
    - Does the resulting data match the ranges selected?
    - Do metrics in the filter get added to the table?
    - Try to break it:  do errors appear in red when we enter an invalid series of things?
2. Edit a Metric
    - Population on open:  Is the "open" state a correct representation of the filter?
    - Edit takes effect, etc.
3. Delete a filter

### The Data Table
1. Does sorting work?  Do metric values appear to be in order?
    - Try both directions!
2. Are there things that shouldn't be here given the current filters?
3. Can I remove columns: metrics and info columns?
4. Can I add columns: info and metric?
5. Did pagination break?
6. Are the watchlist-inlines ([+] in the Symbol column) working?
    - Actually add something to a watchlist.  Try different security types where applicable.
7. Load the table up and scroll. Does everything look okay?
    - Small offset at end is expected
8. Visuals
    - Info columns all left aligned?
    - Metrics Right aligned?
    - Sorted column displayed correctly with arrow pointing in the right direction?

### Export Button
1. Does Exporting Work?
2. Check the file:  Data there? Labels Correct?

### Scoring Models
Run through it.  Do the same as Metric filters + Make sure values look right.

### Saved Screens
1. Did we change anything that would change a param key or value
    - Calc names, info field name?
    - Does anything need to be updated? Was it updated?  There should be data migration.
2. Does it load correctly from empty state?
3. Can we edit it.
   - Goes to unsaved.
   - Save Changes
   - Can load changed screener
   - Check results for all

### Save as Watchlist
1. Create a screen, save the results as a watchlist.  Give it a name.
    - Breaking names: try existing name.
2. Does the watchlist have the same number of items as the screener result?

## Tables ##

### Comp Tables
1. On the first new load of the page, you should see an empty table with "To get started, add securities." popup and the Symbol, Name, and Currency Code columns.
2. Can I add and remove securities: via the securities and list options?
3. Can I add and remove columns: info and metric?
4. Do metric sets work? Can I load, update, and delete them?
5. Can I add and remove summary stats?
6. Does exporting work? How about for the excel add in?
7. Does sorting work?  Do metric values appear to be in order?
    - Try both directions!
8. Load the table up and scroll. Does everything look okay?
    - Small offset at end is expected
9. Visuals
    - Info columns all left aligned?
    - Metrics Right aligned?
    - Sorted column displayed correctly with arrow pointing in the right direction?
10. Does the reset all button work?

### Timeseries Analysis
1. On the first new load of the page, you should see an empty table with "To get started, add securities." popup and the Symbol, Name, Metric and Currency Code columns.
2. Can I add and remove securities: via the securities and list options?
3. Can I add and remove columns: info and metric?
4. Do metric sets work? Can I load, update, and delete them?
5. Can I add and remove summary stats?
6. Does the date range work? (Both start and end dates)
7. Do the data formatting options work?
8. Does exporting work?
9. Does sorting work?  Do metric values appear to be in order?
    - Try both directions!
10. Load the table up and scroll. Does everything look okay?
    - Small offset at end is expected
11. Visuals
    - Info columns all left aligned?
    - Metrics Right aligned?
    - Sorted column displayed correctly with arrow pointing in the right direction?
12. Does the reset all button work?

## Dashboard ##
### Dashboard Watchlists ###

### Dashboard Charts ###


## Popups ##
### Metric ###
1. Open/Close
    - Does it open?
    - Does the (x) close it?
    - Any console messages?
    - Menu Populated? Correct menu options?
    - Does clicking the popup button with the popup up re-open it in a reset state (which can be an "edit mode" preselected state)?  This was a UX decision.  We need to keep this consistent across all popups.
2.  The Search
    - This is often overlooked because there tends to be a search near the popup button but it must be checked!
    - Loader spinner get knocked out of place (THIS HAPPENS A LOT)?
    - Selection closes the menu and works?
    - Again, correct options available?  Is it the right search type?
3. New Selection Mode
    - Does it open the first time with the first option in the first pane selected (second pane displayed)
    - Click through, make sure you can get to the third pane and select.
    - Header and Availability Present?  Periodicity Present?
    - Does the glossary (?) appear when it should?
    - Select a periodicity and click away: Does it reset?
4. Edit Mode
    - Correctly Populated, including periodicity (CHECK ONE WITH A PERIODICITY OPTION).
    - Resubmit, was the full selection correct (try one on pane 2, try one on pane 3 [periodicity])
    - Change the calc: does the selected metric update correctly?
    - Click around, put it in a new state without submitting, make sure menus reset where they should (if on a new calc, the periodicity should return to the first option), but it still reopens with the correct data after no selection.

### Scoring Model ###
_Located on the screener page_

#### Functional Testing 
1. Make sure Type dropdown works correctly
2. Make sure you can select "Saved Scores"
3. Make sure you can edit and save "Saved Scores"
4. Make sure you can create a Score by adding components and changing the weightings.
5. Make sure you can't have invalid weights, negative weights, or any alphanumeric weights.

### Sharing ###
_Need to be using a User that is in a Client Group to share_

#### UI Testing ####
1. Make sure open and closing works properly
2. Make sure changing permissions shows a green "Saved..."
3. Make sure you can switch between all of the permissions

#### Functional Testing ####
1. Share a watchlist with permission set to "View"
2. Share a chart with permission set to "Edit"
3. Unshare a chart by switching permissino to "Cannot View"
3. Login to another user in the same client group
4. Make sure you have the correct permissions for each of the items shared


### Alerts Manger ###

#### UI Testing ####
1. Open and close it ~5 times and see if anything weird happens
2. Does the "x" in the top right work and close the popup?
3. Is it draggable?

#### Functional Testing ####
1. Create an alert for each security type (Equity, MutualFund, Index, Indicator)
    - Are the metrics correct for each security type?
        - Equity should not have "Total Assets Under Management" metric
        - Funds should not have "EBITDA" metric
        - Index should not have "PE Ratio" metric
        - Indicator should not have any metrics.
    - Do Equity, Fund and Index have `System Alert` checkboxes? Do they work?
2. Create a Portfolio Strategy Alert (make sure it appears in Alerts Manager Popup after creation)
    - Do this by going to Data > Portfolio Strategies > Click "Receive Alerts on this Strategy"
3. Create a Watchlist Alert
    - Do this on watchlist by Options > Create/Edit Alerts
4. Edit an alert for each security type (Also test Edit for Watchlist Alerts)
5. Delete an alert for each security type (Also test Delete for Portfolio Strategy Alerts)
6. Does the `Receive Alerts By Email` dropdown have four options of `As Soon As Possible`, `Daily`, `Weekly` and `Never`?
7. Does the `Create New Alert` autocompleter work as expected? (Should show all security types)

### Alerts ###

#### UI Testing
1. Open and close it like 5 times and see if anything weird happens
2. Does the "x" in the top right work and close the popup?
3. Is it draggable?

#### Functionality Testing
1. Create alerts for Fund, Equity, Economic Indicators and Index Watchlists
    - To do so on a watchlist of each type Options > Create/Edit Alerts
2. Create alerts for Fund, Equity, Economic Indicators and Index securities
    - To do so go to each securities page (ie. `/companies/AAPL`) and on the top bar click "Create an Alert"
    - Does each popup have the correct metrics? (ie. A equity should not have "Assets Under Management")
    - Does each popup have Systems Alerts checkbox options?
3. Go to "Alerts Manager" and make sure they were all created successfully

### Info ###
Very similar to other main popups:
1. Opens? Closes? Reopens refreshed on button click while the popup is open?
2. Check search.
3. Correct Menu and Search types?
4. Multi-pane selection works?

### Watchlist Inline ###
1. Does it open? This commonly breaks with changes to security id and type fetching.
2. Is the correct information to get the security present?
3. Can a security be added to a watchlist?
4. Does the popup accurately display what lists the security is already in?
5. Can the security be removed from a list here?
6. Check that the changes being performed in the inline are actually reflected in the watchlist (even if it is just via a refresh and reopening the popup).
7. Be sure to check on quote pages and table situations.

### Security Lists ###
1. Is this multi-security_type?
    - Do the right security groups appear at the top?  Is one selected on open (first)?
    - Changing the selected security type shows appropriate menus?
2. Intersect/Exclude/Add Present?  Should it be?
    - In some cases, it will not appear until after the first list is selected (defaults to add the first time).
    - Check clicking through these. Submit a few.
3. *Watchlists appearing in first pane at the bottom?  Do they work?
4. Edit Mode
    - Make sure it opens in a populated state.
    - Without a change submission, does it reopen to the correct state?
    - Does resubmission work correctly?

## Charts ##
### Fundamental Chart
1. Check that "There are no valid items on this chart. Change or add items from the left sidebar." is displayed on an empty chart.
1. Add securities.
    - Ensure that "Price" was added as a calc for companies if no other companies were on the chart.
    - Ensure that "Level" was added as a calc for indices if no other indices were on the chart.
    - Ensure that "Total Return Price" was added as a calc for mutual funds if no other mutual funds were on the chart.
    - Check that the chart displays the expected data.
1. Add calcs and check that the chart displays the data correctly.
1. Change the zoom.
    - Check that "1d" and "5d" zooms have intraday data for "Price" and "Level" calcs.
    - Check that the "Date Range" inputs update to the correct dates.
1. Change the data format.
    - Change data format to "Normalized (% Change)" and check that all the data starts at 0% on the same date.
    - Change data format to "Percent Off High" and check that all the data looks correct.
1. Change the panel layout.
    - Change panel layout to "Panel per Financial Metric" and there should be a chart for every metric in the left sidebar.
    - Change panel layout to "Panel per Security" and there should be a chart for every security in the left sidebar.
1. Toggle the "US Recessions" chart option on and check that recessions are colored dark grey. (Make sure the chart includes a recession... last one was Dec 07 to May 09)
1. Toggle the "Log Scale" chart option on and check that the data gets logarithmically scaled.
1. Click the "Save Image" button and an image of the chart should be downloaded.
1. Click the "Export Data" button and a csv of the data should be downloaded.
1. Click the "Embed" button and you should see a `<a>` tag that can be copied.
1. Add a correlation of each type and ensure that it appears below and smaller than the main chart.
1. Add a securitylist.
    - Check that the first security in the list appears on the chart.
    - Check that hitting the up and down arrows change the security.
1. Click "Hide Chart Controls" and check that the left sidebar is hidden and the chart increases in size to take the whole page.

#### Saved Charts
1. Save as a new saved chart.
1. Click "Reset All"
1. Open the saved chart and check that all the options are the same.
1. Make a change to the chart and check that the saved chart button says "Unsaved"
1. Save the saved chart.
1. Click "Reset All" and re-open to check that your change was saved.

### Fundamental Chart for Presentation
1. Go through all Fundamental Chart checks.
1. Change size and check that the chart image size changes.
1. Add a title and check that it appears on the chart.
1. Add a note and check that it appears on the chart.
1. Toggle all the chart options.
    - Check that "Display Units" adds units to the label
    - Check that "Display Source" displays the source for economic indicators.
    - Check that "Quote Style Legend" displays price, change, and % change for price and level calcs.
    - Check that "Legend On Chart" toggles where the legend is displayed.

### Technical Chart
1. Check that "To start, choose a stock or index" is display on an empty chart.
1. Check that "Volume", "RSI 14", "SMA 50" and "SMA 200" are added underneath "Add Indicators"
1. Add a security and check that data looks ok.
1. Add/change indicators and check that data looks ok.
1. Change "Chart Style" to "Line" and check that it changes to a line instead of candlesticks.
1. Change "Period" and check that the number of points on the chart changes to the correct period.
1. Check "List Browser" similar to Fundamental Chart.
1. Check "Saved Charts" similar to Fundamental Chart.

### Technical Chart for Presentation
1. Go through Technical Chart checks.
1. Go through Fundamental Chart for Presentation checks.