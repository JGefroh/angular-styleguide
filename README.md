# angular-styleguide

A WIP styleguide for Angular 1.x.

There's a lot of styleguides out there: here's another one based off of my own development practices. Everything in this document is based on my opinion - there are many like it but this one is mine.


## Contents
1. Principles
2. Tooling
3. File and Directories
4. Controllers
5. Services
6. Directives
7. Routing

## Principles
* Consistency  
By having consistent naming and structures, developers that in one module well can more quickly become productive in another module. It also removes much of the errors that occur when things are slightly different.
* Black-box approach  
Developers shouldn't have to know about other modules outside of the one they are working on. They should be able to view the rest of the modules as a black box.
* Don't worry about Angular 2.  
This approach considers Angular 2 a completely different framework. Attempting to reconfigure Angular 1 code to match Angular 2 code is a waste of mental energy and time. If a rewrite is ever required, and the framework you do select is Angular 2, you're going to have to do work anyways. Chances are a better framework will come along before you do.
* Don't worry about ES6.  
This styleguide assumes ES6 doesn't exist. ES6 brings a lot of syntax changes which older browsers won't support without transpiling to ES5. The module system, while useful, is also something I find to be a maintenace burden (having to know file paths, etc.) compared to angular's module system. I'm sure most (if not all) of it can be automated away, but I haven't looked into that. Also, there's fewer developers that know ES6 - it reduces another developer's mental burden of maintaining your programs.

## Tooling
* Use a build tool. 
  * I recommend [brunch](http://brunch.io).
    * Here's a [sample brunch-config](https://gist.github.com/JGefroh/8ae026652a5aecc8c453f89b09317c5b) that matches the folder directory suggested here. 
  * Configure your build tool to:
    * Concatenate dependencies first, followed by `*-module.js` files and all other `.js` files.
    * Manage templates by:
      * placing all `.html` templates into the root folder (can't have duplicate template names if you do this)
      * OR inlining templates via `template:` 
      * OR prepending the folder prefix during build to the file name in `templateUrl: template-file.html` statements.  
* Use [ui-router](https://github.com/angular-ui/ui-router) - it's far superior to the built in routing.
* Use angular's default `$http` unless you have a compelling reason to use libraries like `restangular` or `ngResource`.
  * [My reasoning](http://jgefroh.com/blog/angularjs-use-http/) behind this decision is increased flexibility for low cost.
* Think twice before using auto-annotation tools.
  * I found when people didn't have to manually manage annotations, the number of dependency-injected items went up tremendously. If your team is able to maintain discipline, go ahead and use it to save time and effort!
 
## Files and Directories
* Organize files by modules, not by type
  * Read [Cliff Meyers' blog post](http://cliffmeyers.com/blog/2013/4/21/code-organization-angularjs-javascript) about this.
* File names should be lowercased and [kebab-cased](http://c2.com/cgi/wiki?KebabCase).
  * You avoid a whole class of issues rising from differences in operating systems by doing this.
* File names should match what they contain.
  * `gallery-show-controller.js` should define a controller called `GalleryShowController`.
  * `galleries-service.js` should define a service called `GalleryService`.
  * `thumbnail-listing.js` should define a directive called `thumbnailListing`.
    * Note that directives don't have `-directive` in their names.
### Example folder
```
root/
    app/
        assets/
            images/
                default-user-picture.jpg
            index.html <--- your main HTML page with ng-app and all that good stuff
        modules/
            articles/
            users/
            app.js <--- Your main JS into your application
        scss/
           _articles.scss
           _users.scss
           app.scss
    bower_components/
    node_modules/
    .gitignore
    bower.json
    brunch-config.coffee
    package.json
```

## All
* Wrap all files in [IIFEs](https://en.wikipedia.org/wiki/Immediately-invoked_function_expression).
  * This creates a scope for your variables that is limited to that specific file.
* Use 'use strict';
* Place the angular boilerplate up on top (eg. `angular.module...`).
  * Putting it on the bottom makes it harder to find.
* Use named functions when passing in functions to angular.
* Don't assign angular to anything.

### Good! Do this:
`pun-generator.js`
```
(function() {
  'use strict';
  angular
    .module('module-name')
    .controller('punGenerator', Controller);
    function Controller() {
     //code here
    }
})();
```  
### Bad! Don't do this:
`pun-generator.js`
```
var app = angular.module('module-name');
app.controller('punGenerator', function() {
   //code here
});
```

## Controllers
* The names of page controllers should be PascalCased and end with "Controller", eg. `GalleryShowController`.
* Don't use `ngController` in the DOM except as a root-level controller on `<html>` - all other controllers should be linked with their templates in route definitions or directives.
* Always use `bindToController` and `controllerAs` syntax.
* Controllers should be as small as possible, and mostly serve to manage presentation logic and route requests to Services.
* Controllers should typically handle page-level logic (ie. logic for routable locations).
* Strive for consistency in controller naming.
  * If you have 5 pages that show different things, call all of their Controllers `<module>-show-controller.js` instead of having different names for each.
* Don't use `$scope` unless you need it for events or digests.
  * Use `var vm = this` and add template-usable functions and variables to `vm`.  
 
eg. `users-browse-controller.js`
```
(function() {
  'use strict';
  angular
    .module('myapp.users')
    .controller('UsersBrowseController', ['UsersService', Controller]);
    
    function Controller(UsersService) {
      var vm = this;
      
      initialize();
      
      function initialize() {
        UsersService.query().then(function(users) {
          vm.users = users;
        };
      }
      
      vm.deleteUser = function(user) {
        UsersService.delete(user);
      };
    }
})();
```  

## Services
* Services should contain business logic and calls to 3rd-party systems.
* Use `.service()` exclusively except in extreme circumstances.
* Use `.factory()` to create extendable Services - wrap results in Services.

`base-service-factory.js`
```
(function() {
  angular
    .module('module')
    .factory('BaseServiceFactory', ['$http', Factory]);

  function Factory($http) {
    function createBaseService(arg1, arg2) {
      var service = {};
      //...code here...
      return service;
    }
    return createBaseService;
  }
})();
```
`hello-world-service.js`
```
(function() {
  angular
    .module('module')
    .service('HelloWorldService', ['BaseServiceFactory', Service]);

  function Service(BaseServiceFactory) {
    var service = BaseServiceFactory('hello', 'world');
    service.doExtraStuff = function() {
    };
    //...extra service code...
    return service;
  }
})();
```

## Directives
* Use 2-way binding where appropriate
  * 1-way binding can over-complicate simple things, and 2-way binding is rarely a problem to trace.
* Use isolate scope except in extreme circumstances.
* Directive names must be [camelCased](https://en.wikipedia.org/wiki/CamelCase).
* Never manipulate the DOM in Directive controllers - use the directive's `link` function instead.
* Think reusable when creating directives.
  * If you're going to use something over and over again, make a directive out of it.
* Think self-enclosed, independent functionality when creating directives.
  * It cleans your code up considerable and lets you focus less on the surrounding area.

## Routing
* Use [ui-router](https://github.com/angular-ui/ui-router).
* Have a parent state for every module.
  * You can easily change layouts.
* Use named views - they're incredibly powerful, flexible, and cost little to set up.
* Have a parent state for your entire application.
  * Later on, when you need an application-wide resolve or some other thing, you can add it in one place instead of 20 different places. 
* Place route definitions in their respective `module.js` files.
* Don't be afraid to enhance `$state` via `.run` and give it additional functionality (such as methods to set titles or slugs).
* Use resolves to retrieve data where appropriate.
