### Overview
App State is a Angular 1.x service designed to track the state of certain stateful apps (e.g. charts, screeners, tables) while keep such state in sync with the url params. This service is a little convoluted because it tries to do two things at the same time: 
  1. maintain a **top-level state** (referred as `global state` for the rest of the doc) that all children elements can access; 
  2. sync the state with the url params

[Redux](https://github.com/reduxjs/redux) does the first job perfectly, and the goal is to make Redux sync the url, so it does everything AppState should do.

### What does (the old) AppState do?
  1. Activate a `$routeProvider`, so routing can be performed on non-HTML5 browsers without requesting new pages every time. [See here](https://stackoverflow.com/questions/14319967/angularjs-routing-without-the-hash)
  2. Kicks off a watch on routeChanges to apply the url.
     - For this to work, the locationProvider also needs to be invoked in the config block to declare what hashPrefix to use.
  3. Provide all necessary methods to get and set app-level state, which simply work on the `_data` internal reference.
     - Provide a method to read the url and set state accordingly, and setState from default in case of failure
     - Provide a method to set url for the given state
     - Provide a validator object that can be extended

### The Specifications
  1. Calling AppState's various update methods should apply the change to the global state
  2. Such changes should also be reflected on the url
  3. When url is set for the first time, state will be updated based on the url params, to the extent they are valid, or set to the default value.
  4. When url is manually changed, the global state should change accordingly, if such change is valid (same as above)
  5. A customized default value can be supplied via server side.

### Redux AppState attempts to do the same thing
  1. **Update Url For State Changes**
     - AppState can be configured to listen on changes on a slice of state, and update url params accordingly.
  2. **Update State For Url Changes**
     - AppState can set state when a url is set manually.
  3. **Validation and Fallback**
     - If the manually set url is invalid, AppState should set state to default
  4. **Hydrate with User Default** 
     - [**In Progress**] The **initial** state can be hydrated via server-side, e.g. the user default. **Note this is different from the default fallback value that reducers will return on invalid input**

### On Validation
The validation on step 3 checks for user input (on url), and the goal is to prevent arbitrary data to be put into the url. However, due to the limitation in terms of capacity and performance, not every single detail is verified here. 

For example: the ordered columns of the tables should follow a format of:
```js
  [{type: 'infoField', name: 'security_name'}, ...]
```
What we want to validate on the front end is that the URL params for ordered column is an array of objects with keys of 'type' and 'name', etc; it cannot be a string, or an array of numbers, or array of objects with different keys. i.e. we want to check the JSON schema here. More rigorous validations would require more inputs, e.g. a list of valid info fields, and would take more time, and those should be (and are) performed on the backend.

For example, if the user put in `orderedColumns: [{type: 'infoField', name: 'close_price'}]` when the `close_price` has been renamed to `day_close_price` in the backend, the frontend will not throw away all the ordered columns as it will not know about such changes. It will be up to the backend to valid the actual info field.
