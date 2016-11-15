# Road to AngularJS 2.0


Angular 2.0 is a major revamp of the Angular framework, and as such, introduces a large number of improvements and… breaking changes. It is not backwards compatible with any Angular 1.x built site and requires a significant engineering effort to migrate to. Fortunately, we will not be the first team to migrate to Angular 2.0, so we will be following a prescribed way forward that should introduce change incrementally and enable us to phase and sequence migration work as we would any other kind of project. The nature of the effort will generally adhere to the following guideline:


1) Maximize Angular 1.x upgrade options and position our codebase to be more amenable to the 2.0 migration.

2) Introduce the 2.0 framework into our codebase and utilize Angular-supported mechanisms to incrementally upgrade our apps in a mixed 1.x/2.0 environment until completion.


##Migrating to AngularJS 1.5
In keeping with the above guidelines, we should move to max out our Angular 1.x upgrade path to 1.5 and adopt those 1.5 changes that ease the transition to 2.0. Angular 1.5 introduces new syntax, structural elements and patterns that will be both standard and required in Angular 2.0. Since 1.5 can interpret 1.4 code without modifications, it gives us the opportunity to incrementally up-spec our 1.4 code with only minimal breaking changes. This migration generally addresses 3 things: File System Architecture, Application Architecture and Application Data Flow.


###Migration Case Study, the systems app:

####File System Architecture:
![](https://s3.amazonaws.com/ycharts-static-dev/wiki/directory1.png)

You’ll find that the patterns and structures on blogs or in the Angular docs are a little different from what I’ve done here. Firstly, this is a transitional stage on our way to 2.0, but also we are a Django project...we ain’t some pure single page app. As such, our  main html template and our angular app are in 2 different directories vs. 1 like you’ll find elsewhere. In order to create a link between a single page app’s template and the app’s relevant angular code, I created a directory for the js code that is the same name as the template. Notice command_dashboard.html and command_dashboard/. Let’s check out an angular app:

![](https://s3.amazonaws.com/ycharts-static-dev/wiki/directory2.png)

This is an angular 1.5 app. Well, mostly, and I’ll tell you what I didn’t do and why(django). We know that the top level directory name here matches the django template. That template IS the single page app.Each single page app has at a very minimum, a top level module called app.module.js. There is also, typically, a top level component and template. They would be named app.component.js and app.html. The idea here is that each angular app is a collection of hierarchically pieces, and the top level app.x.x files are the containers for all other js items in subfolders. However, I did not do that. You see, that design prescription expects the django template to exist in the folder as app.html, next to all the other js files. Well, I wasn’t going to change our templating patterns for this, so we have this middle ground above. App.module.js is the app’s entrypoint, defines the module,etc and all other js components add to that module. We’ll speak more about the logical structure of an angular app in a bit. Continue the discussion on directories, each of those subfolders under important_command_dashboard represent a feature of that page. Those feature folders are composed of whatever js components(components, templates, services,etc) are required to work. It is recommended that the feature folders remain flat, even if 1 feature is only ever used by 1 other. Each subfolder in this app represents a feature of that single page app. I wanted to point out another directory structure decision I made:

![](https://s3.amazonaws.com/ycharts-static-dev/wiki/directory3.png)

Shared is not an app, but is represented as a top level directory like the single page apps around it. It is however, a body of code that is shared across multiple angular apps in the systems app. So, in keeping with angular 1.5 structure, I created a shared module that could be added to any single page apps’ app.module.js file. Each app can now access the shared feature subfolder you see above to include that functionality as required. 


####Application Architecture: components, components and all the rest


So, components. What is a component and why have all the system app’s controllers and directives been deleted? A component is the basic building block of an angular app. Everything is a component. There should be 1 top level component, made up of smaller component if required. Component are quite like the directives we knew in many ways since they both create some enclosed, isolated scope over an associated template. However, along with api and lifecycle changes, components are used explicitly in the construction of a modular, component based architecture whereas directives have no such opinion and instead focus on the decoration of the DOM. The idea isn't that one can't do what the other does, but instead is a about what you SHOULD do with one versus the other. It turns out that most of our directives will become components and very few of them will remain directives. A key tactical difference is that components are emplaced in a template hierarchically as custom elements, whereas directives are added as attributed to existing elements. Again, directives decorate. I’ve selected a few components to analyze. I chose 2 components to look at not because they are the best pieces of angular 1.5 I can produce, but instead because they represent what our transitional 1.5 world will look like as we try to account for new component based architecture and our current deep dependence on $scope.

```python
shared.component('ycManagementCommandPopup', {
    require: {
        ycAutoCloseCtrl: 'ycAutoClose'
    },
    bindings: {
        commandRunId:'<',
        popupCloseStateHandler:'&'
    },
    templateUrl:'/media/systems/js/shared/management_command_popup/management_command_popup.html',
    controller: function(Ajax, $sce, $scope){
        
        // you dont need to do this, this is cool most of the time
        // unless you're in a scoping circumstance where this can conflict
        var ctrl = this;
        
        ctrl.$onInit = function() {
            ctrl.ycAutoCloseCtrl.setAutoCloseHandler(function(){
                // note that calls into this here, call the autoclose directive
                if(ctrl.isOpen){
                    ctrl.closePopup();
                    $scope.$apply();
                }
            });

            ctrl.isOpen = false;
        };

        // no more scope watches... this is how you detect one way changes
        ctrl.$onChanges = function(changes){
            if (changes.commandRunId){
                if (changes.commandRunId.isFirstChange()) return;
                if (!changes.commandRunId.currentValue) return;
                ctrl.commandRunId = changes.commandRunId.currentValue;
                Ajax.Systems.CommandRun.get({},{'id': ctrl.commandRunId})
                    .success(function(data){
                        ctrl.isOpen = !ctrl.isOpen;
                        ctrl.output = $sce.trustAsHtml(data.output);
                        ctrl.scriptName = data.script_name;
                        ctrl.runtime = data.runtime;
                        ctrl.endtime = data.end_time;
                    })
                    .error(function(error, status){
                        alert('Please make sure the commandRunId is correct.');
                    });
            }
        };

        // called when user hits the X on the popup
        // and when someone clicks away from the popup
        ctrl.closePopup = function() {
            ctrl.isOpen = false;
            ctrl.popupCloseStateHandler();
        };

    }
});

```















































Component in Angular 1.5 look good. There are some noticeable interface changes where everything is basically an object and can be treated as such. In fact, if the controller code here was any bigger, I could have chosen to define it in manage_command_popup.controller.js and instead just pass controller a class name instead. That’s the beauty of feature folders and component based architecture; it grows as the code does across multiple files and has the right mechanisms to bring pieces together here. Elements of note:
 Bindings: this used to be our scope interface. Here, you’ll mostly notice things being 1-way data bound in or strings and callbacks. Also, anything bound here get attached to the controller object automatically.
Controllers - components have them and you won't see ng-controller hanging around anymore.That means that there are no longer module level controllers… they must be in a component. This lets them take advantage of all the code organization features of a component, but also lets us just write them as javascript. When controller code is just javascript, you can share them across components as required. Controllers now have special lifecycle methods, sorta equivalent to a mix of what we used to do in scope methods and in a directive link method. You can see above $onInit and $onChanges. $onInit is like the __init__ in python, controller object initialization. You’ll set up things you’ll need for the template here, make api calls, etc. Code we used to do in a directive’s link scope, we’ll do here instead. $onChanges is like our old $scope.watch… but exclusively for 1 way data bound values. It fires when changes occur, and we have a new api to observe and react to those changes. You’ll also notice this line var ctrl = this. Firstly, that redefinition is to keep us out of trouble in closures, etc where this changes meaning. Also, anything you attach to ctrl will be visible in the template. It’s like attaching thing to $scope in our pre 1.5 way of doing things.  In templates, you’ll see this:

No more calling methods and attributes from scope space, we ask for things directly from $ctrl.The key things with controller is that there is a lifecycle to hook into and operations we used to perform on scope, will be performed on ctrl.
    3) 







I included this component just to demonstrate further what this mix of old world and new world look like. We replace scope with ctrl in so many ways, but until everything is switched over, we’re left with this mix. Notice the the $scope.$on here and calls to AppState. They still work. 


####Application Data Flow: one-way data binding


Angular 1.5 introduces a new standard of data flow through an angular project. Gone is 2-way data binding, and welcome 1 way data binding + callbacks. Fortunately, as evident above, this really isnt so bad. Firstly, many of our 2-way data bound scope vars are actually just used 1 way… we just use the = notation.If that’s the case, the above is a perfect model of how we will transition. Scenarios in our code where a child directive and parent entity actually rely on 2-way data binding we could...keep it. OK, keeping it still works in 1.5, but it HAS to go. We’ll do very nearly the same as above except bind in a function from parent scope that is called every time the the variable of interest changes.


###Resources:

####General Overview:
https://docs.angularjs.org/guide/migration
https://github.com/toddmotto/angular-styleguide
https://teropa.info/blog/2015/10/18/refactoring-angular-apps-to-components.html

####Component and file system architecture:
https://www.sitepoint.com/building-angular-1-5-components/
https://toddmotto.com/exploring-the-angular-1-5-component-method/
https://scotch.io/tutorials/how-to-use-angular-1-5s-component-method
https://docs.angularjs.org/guide/component
https://github.com/toddmotto/angular-1-5-components-app
https://angular.io/docs/ts/latest/guide/style-guide.html#!#application-structure (angular 2)

####One-way Data Binding:
https://toddmotto.com/one-way-data-binding-in-angular-1-5/

###What we need to do:
Upgrade AngularJS to 1.5 with zero code changes and test for potential breaking changes.
Establish across the team the new, Angular 1.5 methods of organizing the file system, building components, one way data binding, etc so that new development adheres to these patterns.
Sequence, spec and schedule appropriate units of code to migrate.

##Migrating to AngularJS 2.0
