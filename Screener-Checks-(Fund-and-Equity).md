# Checking a Screener PR

## Key Components
* Universe Filters
* Metric Filters
* Data Table
* Export Data
* Metric/Info Columns
* Scoring Models
* Saved Screens
* Save as Watchlist

## Testing
Below are some steps to test the key components above.  Those without their own section are handled incidentally in checking other things.  A full pass through may not be necessary, but keep in mind where metric changes might come into play in unexpected ways (columns, sorting, saved screens etc), calc menu changes might cause problems (popups), etc.

## Security Universe Filters
These allow us to filter down by security lists.  Default should be all of the asset type.
### Things to Test
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

## Metric Filters
These allow us to filter by an individual metric value, a range, etc.
### Things to Test
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

## The Data Table
This is where the data appears.  Handles display, of course, adding and removing items, and sorting.  Check the following:
### Things to Test
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

## Export Button
Export to CSV
### Things to Test
1. Does Exporting Work?
2. Check the file:  Data there? Labels Correct?

## Scoring Models
### Testing
Run through it.  Do the same as Metric filters + Make sure values look right.

## Saved Screens
Saved screener params
### Testing
1. Did we change anything that would change a param key or value
    - Calc names, info field name?
    - Does anything need to be updated? Was it updated?  There should be data migration.
2. Does it load correctly from empty state?
3. Can we edit it.
   - Goes to unsaved.
   - Save Changes
   - Can load changed screener
   - Check results for all

## Save as Watchlist
Saves the screener result set as a new watchlist.
1. Create a screen, save the results as a watchlist.  Give it a name.
    - Breaking names: try existing name.
2. Does the watchlist have the same number of items as the screener result?

