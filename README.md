# Angular Style Guide

## High Level Goals

1. Prioritise readability: code is read more than it is written.
2. Be explicit, not implicit.
3. Know [when to deviate](http://legacy.python.org/dev/peps/pep-0008/#a-foolish-consistency-is-the-hobgoblin-of-little-minds) from the style guide. 
4. Composability > inheritance.
5. Embrace the future of web application development – ES6 and Angular 2.0.

---

## Directories & Files

- Rule
  There are two forms of organising code: components and routes. A component is a reusable piece of UI that contains all the HTML, CSS, and JavaScript required for it to work. A route is a collection of components and non-reusable pieces of UI bound to a URL.

- _Why_:
- _Caveats_:
- Code example

- Structure
  - Modules
- Naming
  - '.' convention (.factory, .controller etc.)

---

### Helpers
[Pure](http://en.wikipedia.org/wiki/Pure_function) functions. For example, `currencyFilter`.

### Components
Encapsulated DOM components.

### Services
Business logic.

---

## Parts of Angular

### Routes

#### Rules:
  1. Use resolvers to inject data.
  _Why_: Insures the page is rendered only when all data is available.
  2. Use query parameters to store route state. For example, the current `offset` and `limit` when paginating.
  _Why_: The current view should be represented in the URL.

### Directives

#### Rules:
  1. Use element directives when content is injected. Else use attribute directives.
  _Why_: Separates responsibility: element directives add content, attribute directives add behaviour, and class attributes add style.
  2. Use an isolate scope for _element_ directives. Use inherited scope for _attribute_ directives.
  _Why_: Using an isolate scope forces you to expose an API by giving the component all the data it needs, making it reusable and testable. Attribute directives should not have an isolate scope because doing so clobbers the current scope.
  3. When using isolate-scope properties, always `bindToController`.
  _Why_:
    3.1. It explicitly shows what variables are shared via the controller.
    3.2.

### {Anti-}Patterns
  - Pattern:
  - Anti-pattern:
    - Only reference the present component’s scope
    - _Why_: Makes components reusable, comp

### Controllers

#### Rules:
  1. Use [`controllerAs`](http://toddmotto.com/digging-into-angulars-controller-as-syntax/) syntax.
  _Why_:
    1.1. It explicitly shows what controller a variable belongs to, by writing `{{ ctrl.foo }}` instead of `{{ foo }}`
  2. Inject ready data instead of loading it in the controller.
  _Why_:
    2.1. Simplifies testing with mock data.
    2.2. Separates concerns: data is resolved in the route and used in the controller.
  3. Extend a controller’s properties onto the controller.
  _Why_: What is being exported is clear.
  4. Only extend the controller with properties used in templates.
  _Why_: Adding unused properties to the digest cycle is expensive.
  5. Store presentation logic in controllers and business logic in services.
  _Why_:
    5.1. Simplifies testing business logic.
    5.2. Controllers are glue code, and therefore require integration tests not unit tests.
  6. Only instantiate controllers through routes or directives.
  _Why_: Allows reuse of controllers and encourages component encapsulation.

### Modules

#### Rules:

  1. Create one module per file and don’t alter a module other than where it is defined.
  _Why_:
    1.1. Prevents polluting the global scope.
    1.2. Simplifies unit testing by declaring all dependencies needed to run each module.
    1.3. Negates necessity to load files in a specific order.

  2. Use ES6 module system and reference other modules using Angular Module’s `name` property.
  _Why_:
    2.1. Encapsulates all required files, making unit testing easier and error feedback more specific.
    2.2. Simplifies upgrading to Angular 2.0, which uses ES6 modules.
    

---

## Patterns
- 

## Anti-Patterns
- Using the `$` name space in property names.
- 

---

## Further reading
- [Component-based software engineering](http://en.wikipedia.org/wiki/Component-based_software_engineering)

---

# Template
- Rule
- Why
- Caveats
- Code example
