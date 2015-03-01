## Stateful Apps

Apps that have "state", as we refer to it, are those that update their UI/data based on the querystring after the hash in the URL. Examples of current stateful apps are the [Fundamental Chart](http://ycharts.com/charts/fundamental_chart), [Stock Screener](http://ycharts.com/stock_screener), and [Data Tables](http://ycharts.com/export_center/multicompany_comparison).

### Getting Started Checklist
* Write a `MyNewAppState` service that extends `AppState` (see `FundChartState` for an example) and make sure it has a `validate()` function.
* Call `AppStateProvider.enableState()` and `AppStateProvider.setDefaults({...})` in the `config` block for the app module (in-line in the Django template)
* Write a primary controller for the app, `MyNewAppCtrl`, that responds to the `"urlChanged"` event, calls `validate()` on the state service, and changes urlHash if the state is valid (see example in the "Controllers" section of this doc).
* For a state-dependent UI element, write a directive that injects `MyNewAppState` and reads from it whenever urlHash changes. Whenever a user action takes place (e.g. click event), call a handler that the containing controller defines.

### Services
All stateful apps make use of the `AppState` service, which is responsible for synchronizing params in the URL with an internal object that stores the current state of the app. Each app should have its own, specific state management service which extends `AppState`, defines a `validate()` function, and optionally defines any additional state management functions.

For example, the Fundamental Chart app has a `FundChartState` service which defines an `addSeries()` function responsible for adding unique chart series objects to the state. The `validate()` function should set any invalid state params (e.g. a 100 year chart zoom) to their default values and return `true` if everything was valid and `false` if there were any invalid params.

The `AppState` service broadcasts the `"urlChanged"` event on the `$rootScope` whenever the URL/state data changes.

If you need to parse/translate URL params in your app, you can use the `url` functions defined in the `Utils` service, which the `AppState` service also uses.

### Controllers
Apps can have multiple controllers, but should have a single "primary" controller (e.g. `FundChartCtrl`) that listens for the `"urlChanged"` event which gets broadcasted by `AppState`. To handle this event, the controller should check if the new/current state is valid; If it is __valid__, it should perform any necessary tasks and then changes urlHash; If it is __invalid__, it should update the URL from the state, which will have just been corrected (using defaults wherever necessary) by the `validate` function. The urlHash changed event is handled by directives which are discussed next.

Here's an example of the state event handling in `FundChartCtrl`:

```
$scope.$on('urlChanged', function(){
    if(FundChartState.validator.validate()){
        updateScopeFromState();
        $rootScope.urlHash = FundChartState.getUrlHash();
    } else {
        FundChartState.setUrlFromState();
    }
});
```

Controllers should define handler functions which directives can call to pass data to them. For example, the `FundChartCtrl` defines `$scope.onZoomChanged(zoom)` which is passed into the `ycChartZoom` directive as an `&` executable, isolated scope variable. Whenever the user clicks on a zoom option, the zoom directive calls this handler, passing in the selected option, and the controller then updates the state by making the necessary call to `FundChartState`.

### Directives
Most UI elements of apps (e.g. zoom button group in Fundamental Chart, sliders in Stock Screener, etc...) will be built as Angular directives, typically with isolated scopes. The directives can watch for changes to urlHash, which is changed by the controller when state changes, so that they can update to reflect the current state (e.g. the zoom directive may highlight a different zoom option after the state changes). Directives that deal with state should inject their app's respective state service and *read* from it when the state changes, but should NOT write to state directly. Only controllers should write to state.

### App Flow
![Angular app flow diagram](http://i.imgur.com/OPp8rcE.png)

## Gotchas / Weird things that are not obvious
* DOM event handlers (e.g. jQueryUI slider callback) run outside of the Angular execution context, so if you need to update the scope within one of these handlers, you need to call `scope.$apply()` afterward.
* In a directive, if you try to change an isolated scope `@` variable within a `$watch` that is going to be immediately fired, for some reason the change you're trying to make may not be applied. Not sure if this is an Angular bug or some advanced concept we don't fully understand. The simple solution is to have a separate variable be the main value and the isolate scope variable just be the initial/default. See `currentHandleLabel` vs. `handleLabel` in `ycBasicDropdown` for an example.
* Sometimes you may need to interact with the scope, allow the DOM to update as a result, and then do something immediately afterward. I'm still learning the intricate details of how this works, but basically there has to be a digest loop that completes before you can be confident the DOM has been updated/synced with the scope. You can use $timeout with a delay of 0, or potentially scope.$evalAsync (again, still don't fully understand how this works or how to use it).
* Several built-in directives (e.g. ngIf, ngRepeat, etc...) create child scopes. If you try to *write* to a parent scope variable from within a child scope, you'll instead create a completely separate reference in the child and you won't be touching the parent. This is because Angular scopes prototypically inherit from each other, as explained in detail [here](http://stackoverflow.com/questions/14049480/what-are-the-nuances-of-scope-prototypal-prototypical-inheritance-in-angularjs). See the bottom of the [alerts modal control template](https://github.com/ycharts/ycharts/blob/develop/templates/alerts/js/angular/alerts_modal_control.html#L79) for an example where `$parent` needs to be used to write to the `displayModal` parent scope variable. An alternative is to store parent scope variables in an object (e.g. `$scope.someObj = {displayModal: false};`) and then write to `someObj.displayModal` in the child scope, which will work.

## Testing

**Karma is for unit tests only at this point!**

* Protractor will replace karma as the e2e test runner with 1.2.
* Angular scenarios will also be dead.

Tests are executed through Karma Test Runner (also known as Testacular).

    # for unit tests
    /sites/ycharts/node_modules/karma-cli/bin/karma start confs/karma/karma-unit.conf.js

## General Directive Structure

Directives should be structured in the following order within the link:

* Scope Vars
* Internal Vars
* Internal Functions
* Watch/action of the directive
* Scope functions
* Reset if necessary at bottom

We do not use a leading underscore to mark private functions. So none of this:

        NO!  var _getData = function(){};  NO!
        YES! var getData = function(){};   YES!

## Angular in Templates
In the template, elements should be constructed as consistently as possible.  In general, follow:

* ng-if, ng-repeat, ng-show, ng-switches, etc.
* Id
* Class
* Element-specific items: value, type, etct.
* Other Angular items

Yes:

     <th id="uniqueMetric" class="colMetrics" scope="row" ng-bind="calc.calc_label"></th>

If possible, organize directive attributes alphabetically.
