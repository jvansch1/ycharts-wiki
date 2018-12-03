Notes for Lucas, Kdab and Fox.

#### Add Metric Filter 

1. Percentile tab. 
    1. Implement the tab so that everything functions as expected except for the equation field will just be an autocompleter for now. 
    1. That means that the state of the metric filter should still work perfectly fine with the correct left, middle and right values
1. Rank Tab
    1. Same as percentile tab in terms of steps
1. Range Tab
    1. Implement everything except for the equation field (which will just be an autocompleter for now)
    1. Implement the slider component 
        - Slider componenet should contain the actual "slider" and the left and right inputs.
        - it should have a callback that gets fired when either the slider moves or the left or right inputs are changed.
1. Metric Equation Service 
    1. Build a purely js (no need for angular) class that can validate an equation (represented as an array of strings and integers)
    1. Write some sweet unit tests for this service that test all of the invalid and valid states of an equation.
1. Metric Equation Component (MVP)
    1. Start with just the ability to build sequentially forward (not editing in place, etc)
    1. Implement the logic that validates the equation at every change. This also means getting the CSS / messaging on the component correct. 
    1. In general, when looking building it sequentially look at what we are currently doing in the metric filter.
    
 


- Think about breaking some of the logci up (when it feels right) to services, components, etc
- Mapping middle of metric filter object to tab (on edit when opened with state)


