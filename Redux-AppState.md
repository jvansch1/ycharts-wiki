App State is a Angular 1.x service designed to track the state of certain stateful apps (e.g. charts, screeners, tables) while keep such state in sync with the url params. This service is a little convoluted because it tries to do two things at the same time: 
  1. maintain a top-level state that all children elements can access; 
  2. sync the state with the url params

Redux does the first job perfectly, and the goal is to make Redux sync the url, so it does everything AppState should do.

What does AppState do?
  1. Activate a `$routeProvider`, so routing can be performed on non-HTML5 browsers without requesting new pages every time. [See here](https://stackoverflow.com/questions/14319967/angularjs-routing-without-the-hash)
  2. Kicks off a watch on routeChanges to apply the url. // I don't really get this part.
  3. Provide all necessary methods to get and set app-level state, which simply work on the `_data` internal reference.
     - Provide a method to read the url and set state accordingly, and setState from default in case of failure
     - Provide a method to set url for the given state
     - Provide a validator object that can be extended
