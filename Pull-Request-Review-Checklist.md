# Pull Request Review Checklist #

## Basic ##

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

## Popups ##
### Metric ###
### Alerts Manger ###

#### UI Testing
1. Open and close it ~5 times and see if anything weird happens
2. Does the "x" in the top right work and close the popup?
3. Is it draggable?

#### Functional Testing
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
### Watchlist Inline ###
### Security Lists ###

## Charts ##
