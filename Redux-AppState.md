App State is a Angular 1.x service designed to track the state of certain stateful apps (e.g. charts, screeners, tables) while keep such state in sync with the url params. This service is a little convoluted because it tries to do two things at the same time: 
  1. maintain a top-level state that all children elements can access; 
  2. sync the state with the url params

Redux does the first job perfectly, and the goal is to make Redux sync the url, so it does everything AppState should do.