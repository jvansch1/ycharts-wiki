App State is a Angular 1.x service designed to track the state of certain stateful apps (e.g. charts, screeners, tables) while keep such state in sync with the url params. This service is a little convoluted because it tries to do two things at the same time: 
  1. maintain a **top-level state** (referred as `global state` for the rest of the doc) that all children elements can access; 
  2. sync the state with the url params

[Redux](https://github.com/reduxjs/redux) does the first job perfectly, and the goal is to make Redux sync the url, so it does everything AppState should do.

What does AppState do?
  1. Activate a `$routeProvider`, so routing can be performed on non-HTML5 browsers without requesting new pages every time. [See here](https://stackoverflow.com/questions/14319967/angularjs-routing-without-the-hash)
  2. Kicks off a watch on routeChanges to apply the url.
     - For this to work, the locationProvider also needs to be invoked in the config block to declare what hashPrefix to use.
  3. Provide all necessary methods to get and set app-level state, which simply work on the `_data` internal reference.
     - Provide a method to read the url and set state accordingly, and setState from default in case of failure
     - Provide a method to set url for the given state
     - Provide a validator object that can be extended

The Specifications are summarized as below:
  1. Calling AppState's various update methods should apply the change to the global state
  2. Such changes should also be reflected on the url
  3. When url is set for the first time, XXXXX
  4. When url is manually changed, the global state should change accordingly, if such change is valid