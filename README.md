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
### DO use a build tool. 
* I recommend brunch.
### Configure your build tool to:
* Concatenate dependencies first, followed by `*-module.js` files and all other `.js` files.
* Manage templates by:
  * placing all `.html` templates into the root folder
  * OR inlining templates via `template:` 
  * OR prepending the folder prefix during build to the file name in `templateUrl: template-file.html` statements.  
  
### DO use ui-router.  
### DO use $http.  
 
## Files and Directories


### Organize your angular-related files by modules.
```
root/
__________app/
_______________modules/
____________________articles/
_________________________articles-module.js
_________________________article-preview.js
_________________________article-preview.html
_________________________articles-service.js
_________________________articles-show-controller.js
_________________________articles-show.html
____________________components/
_________________________loading-indicator.js
_________________________picture-button.js
____________________galleries/
_________________________galleries-module.js
_________________________gallery-thumbnail.js
_________________________gallery-thumbnail.html
_________________________galleries-service.js
_________________________galleries-browse-controller.js
_________________________galleries-browse.html
bower-components/
node_modules/
bower.json
package.json
.gitignore
etc.
```

DON'T:
* Separate files by type (eg. controllers in `controllers/`, services in `services/`.

### File Names
All file names should be lowercased and kebab-cased.
```
loading-indicator.js
potato.html
```

### Components / Directives shouldn't have the word `directive` at the end.
```
gallery-module.js
gallery-show-controller.js
gallery-show.html
gallery-listing.js
gallery-listing.html
```

### File names should match Controller / Directive names.
eg. `gallery-show-controller.js`
```
(function() {
  angular
    .module('galleries')
    .controller('GalleryShowController', [Controller]);
    //...rest of code...
```

eg. `gallery-listing.js`
```
(function() {
  angular
    .module('galleries')
    .directive('galleryListing', [Directive]);
    //...rest of code...
```

## Controllers
### The names of page controllers should be PascalCased and end with "Controller".
eg. `gallery-show-controller.js`
```
(function() {
  angular
    .module('galleries')
    .controller('GalleryShowController', [Controller]); //Note the PascalCasedControllerName.
    //...rest of code...
```

### Don't use `ng-controller`.
* Controllers should always be linked with their associated template through the router.
* Always use `bindToController` and `controllerAs: 'vm'` syntax.

## Services
### Use `.service()` exclusively except in extreme circumstances.
### Use `.factory()` to create extendable services - wrap results in services.
`base-service-factory.js`
```
(function() {
  angular
    .module('hnlio.base')
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
`gallery-service.js`
```
(function() {
  angular
    .module('hnlio.components')
    .service('EventsService', ['BaseServiceFactory', Service]);

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
### Use 2-way binding.
### Use isolate scope except in extreme circumstances.
### Directive names should be camelCased.
