# angular-styleguide

A WIP styleguide.

There's a lot of styleguides out there: here's another one based off of my own development practices.


## Contents
1. Principles
2. Tooling
3. File and Directories
4. Controllers
5. Services
6. Directives

## Principles
* Consistency
* Black-box approach

## Tooling
* Use a build tool. 
  * I recommend [brunch](http://brunch.io).
  * Configure your build tool to:
    * Concatenate dependencies first, followed by `*-module.js` files and all other `.js` files.
    * Manage templates by:
      * placing all `.html` templates into the root folder
      * OR inlining templates via `template:` 
      * OR prepending the folder prefix during build to the file name in `templateUrl: template-file.html` statements.  
* Use [ui-router](https://github.com/angular-ui/ui-router).
* Use angular's default `$http` unless you have a compelling reason to use libraries like `restangular` or `ngResource`..
 
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


## Controllers
* The names of page controllers should be PascalCased and end with "Controller", eg. `GalleryShowController`.
* Don't use `ngController` in the DOM except as a root-level controller on `<html>` - all other controllers should be linked with their templates in route definitions or directives.
* Always use `bindToController` and `controllerAs` syntax.

## Services
* Use `.service()` exclusively except in extreme circumstances.
* Use `.factory()` to create extendable services - wrap results in services.
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
