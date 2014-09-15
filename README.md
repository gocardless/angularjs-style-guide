# GoCardless AngularJS Style Guide

Repo: https://github.com/gocardless/gc-angularjs-styleguide (will be made public eventually)

# Definitions

Word definitions..?

# Design principles

General principles (e.g. suit)

# Naming files, folders and Angular modules

Every concern/single responsibility is self contained within its own folder.

```
/app
  /routes
      /about
  /constants
  /config
  /components/popover (ui web components)
  /services (interact with anything external)
  /helpers (filters..)
  /helpers/spec/unit
  /helpers/spec/e2e
```

Filenames are lowercase with dashes in place of spaces. Function name is camel

Angular modules are prefixed with `gc.`

```js
// routes
gc.routes.home.index
gc.routes.home.show

Top level folders: routes, config, constants, components, services, helpers

Routes: index, show, create, update

Dot name spaces:
    - route, config, constant, component, service, helper
    - controller, template

gc.routes.home.index

    /routes
        /home
            /index
                /home-index.route.js (gc.routes.home.index, HomeIndexRoute)
                /home-index.controller.js (gc.routes.home.index.controller, HomeIndexController)
                /home-index.template.html

// config
gc.config.locationProvider

    /config
        /location-provider
            /location-provider.config.js

// constants
gc.constants.api

    /constants
        /api
            /api.constant.js

// components
gc.components.dialog

    /components
        /dialog
            /dialog.component.js (gc.components.dialog, Dialog, gcDialog)
            /dialog.controller.js
            /dialog.template.html

// services
gc.services.users

    /services
        /users
            /users.service.js (gc.services.users, UsersService)

// helpers
gc.helpers.currencyFromPence

    /helpers
        /currency-from-pence
            currency-from-pence.helper.js (gc.helpers.currencyFromPence, CurrencyFromPenceHelper)
        /currency-from-pence-filter
            currency-from-pence-filter.helper.js (gc.helpers.currencyFromPence, CurrencyFromPenceHelper)
```

## Angular Modules

Rather than have one module for our application and attach all components, we keep every component in its own module. For example, the home page controller is defined in the `gc.home.index.controller`.

Our main application then depends on the top level modules:

```js
angular.module('gc.main', [
    'gc.home',
    'gc.users',
    ...
]);
```

## Routes

A route folder will usually have the following files in:

- home-index-controller.js
- home-index-controller.spec.js
- home-index-route.js
- home-index-template.html
- home-index.e2e.js

_Why_: we've found because Angular components tend to be isolated, storing all parts of a component in the same folder makes more sense than splitting up the app into folders for controllers, templates, and so on.

## Constants

## Config

## Services

## Components

## Helpers

# ES6

### Loading files with ES6 Module Loader

We use [Systemjs](https://github.com/systemjs/systemjs) to load in dependencies, using the ES6 Module specification. Any dependencies of a file (a controller will often depend on a service, for example), are imported:

```js
import '/app/shared/services/current-user-service';
```

## Controllers

### Instantiating controllers

**controllerAs View Syntax**: Use the controllerAs syntax over the classic controller with $scope syntax.

Why?: Controllers are constructed, "newed" up, and provide a single new instance, and the controllerAs syntax is closer to that of a JavaScript constructor than the classic $scope syntax.

Why?: It promotes the use of binding to a "dotted" object in the View (e.g. customer.name instead of name), which is more contextual, easier to read, and avoids any reference issues that may occur without "dotting".

Why?: Helps avoid using $parent calls in Views with nested controllers.

Avoid coupling controllers in views, couple controllers to templates in the route or directive.

```html
<!-- avoid -->
<div ng-controller="Customer">
  {{ name }}
</div>

<!-- avoid -->
<div ng-controller="Customer as customer">
  {{ customer.name }}
</div>
```

```js
/* avoid */

// page.route.js
angular.module('page', [])
  .config(function config($routeProvider) {
    $routeProvider
      .when('/avengers', {
        templateUrl: 'avengers.html',
        controller: 'Avengers'
      });
  });

/* recommended */

// page.route.js
angular.module('page', [])
  .config(function config($routeProvider) {
    $routeProvider
      .when('/avengers', {
        templateUrl: 'avengers.html',
        controller: 'Avengers',
        controllerAs: 'avengers'
      });
  });
```

### Writing controllers

The `controllerAs` syntax uses `this` inside controllers which gets bound to $scope

Why?: `controllerAs` is syntactic sugar over `$scope`. You can still bind to the View and still access `$scope` methods.

Why?: Helps avoid the temptation of using `$scope` methods inside a controller when it may otherwise be better to avoid them or move them to a factory. Consider using `$scope` in a factory, or if in a controller just when needed. For example when publishing and subscribing events using `$emit`, `$broadcast`, or `$on` consider moving these uses to a factory and invoke from the controller.

```js
/* avoid */
function Customer($scope) {
  $scope.name = {};
  $scope.sendMessage = function sendMessage() { };
}

/* avoid */
function Customer() {
  this.name = {};
  this.sendMessage = function sendMessage() { };
}

/* recommended - see next section */
function Customer() {
    _.extend(this, {
        currentUser: user,
        createNewUser: createNewUser,
        ...
    });
}
```

### Extending `this`

Using [Lodash's `extend` method](http://lodash.com/docs#assign), we export properties into the scope/this at the bottom of each controller:

```js
_.extend(this, {
    currentUser: user,
    createNewUser: createNewUser,
    ...
});
```

_Why:_ Rather than have `$scope.user = ...` (or `this.user = '..'`) scattered throughout a controller, which makes it difficult to see what the controller makes available, we do it all in one place, and make sure it's always at the bottom of the controller. This makes it easy at a glance to see what the controller exports to the scope.

### Compose controller logic

```js
/* avoid */
function Order ($http, $q) {
  function checkCredit () { 
    var orderTotal = vm.total;
    return $http.get('api/creditcheck').then(function (data) {
        var remaining = data.remaining;
        return $q.when(!!(remaining > orderTotal));
    });
  };

  _.extend(this, {
    checkCredit: checkCredit
  });
}

/* recommended */
function Order (creditService) {
  function checkCredit () { 
    return creditService.check();
  };

  _.extend(this, {
    checkCredit: checkCredit
  });
}
```

### Syntax

We use the more verbose syntax for defining controllers and their dependencies.

```js
angular.module('gc.home.index.controller', [
]).controller('HomeIndexController', [
    '$http',
    '$scope',
    function HomeIndexController($http, $scope) {
    }
]);
```

_Why:_ This lets us minify without dependency injection, and without relying on a tool like ng-annotate.

### Resolvers

Any data that the controller always needs are resolved in the route and injected into the controller. See the next section, "Routing and Resolvers" for more on this.

## Routing and Resolvers

### ui-router

We prefer [ui-router](https://github.com/angular-ui/ui-router) to Angular's `$ngRoute`. We find the ability to nest views and ui-router's approach to managing routes as state suits our workflow and our applications.

### routes

Each route is defined within its own module. Often it will inherit from another route (for example, we have an `authRequired` route which ensures a user is logged in):

```js
angular.module('gc.webhooks.index', [
    'ui.router',
])
```


### resolvers



## Services

## API Calls

## Testing

## Directives

## Dependency Management and Modules

## Use of ES6 Features

## JSHint

## Code Patterns

