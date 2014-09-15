#GC AngularJS Style Guide

Repo: https://github.com/gocardless/gc-angularjs-styleguide (will be made public eventually)

## File and Directory Structure

Every component is self contained within its own folder. We split our components up into two categories:

- pages
- shared

### Shared Components

Shared components include any directives, services, filters, or any file that is required by many components. 

### Controllers and Views

Pages are where all our controllers are stored. Controllers are nested based on the route they are accessed at. For example, the home page index controller is at the following path:

```
/app/pages/home/index/home-index-controller.js
```

A controller folder will usually have the following files in:

- home-index-controller.js
- home-index-controller.spec.js
- home-index-route.js
- home-index-template.html
- home-index.e2e.js

_Why_: we've found because Angular components tend to be isolated, storing all parts of a component in the same folder makes more sense than splitting up the app into folders for controllers, templates, and so on.

### Loading files with ES6 Module Loader

We use [Systemjs](https://github.com/systemjs/systemjs) to load in dependencies, using the ES6 Module specification. Any dependencies of a file (a controller will often depend on a service, for example), are imported:

```js
import '/app/shared/services/current-user-service';
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

## Controllers

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

### Extending `$scope`

Using [Lodash's `extend` method](http://lodash.com/docs#assign), we export properties into the scope at the bottom of each controller:

```js
_.extend($scope, {
    currentUser: user,
    createNewUser: createNewUser,
    ...
});
```

_Why:_ Rathern than have `$scope.user = ...` scattered throughout a controller, which makes it difficult to see what the controller makes available, we do it all in one place, and make sure it's always at the bottom of the controller. This makes it easy at a glance to see what the controller exports to the scope.

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

