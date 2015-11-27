# React / Redux Style Guide

This is an opinionated style guide for developing applications in ES6+ with React and/or Redux.

These styles are based on current best practices in the [React] and [Redux] communities, as well as real life experience
with these tools in the field. It puts great focus on writing readable and maintainable code and using new JavaScript
features in a responsible manner.

[React]: https://facebook.github.io/react/
[Redux]: redux.js.org

## Credit

Much of this style guide is based on the Redux documentation and ideas from [Dan Abramov] in particular. Furthermore, it
was inspired by the [Angular Style Guide] by John Papa.

[Dan Abramov]: https://github.com/gaearon
[Angular Style Guide]: https://github.com/johnpapa/angular-styleguide

## Contributing

This guide is opinionated. You'll probably disagree on some points. Feel free to [open an issue] or file a pull request
if you think something should be rephrased or changed. Please include a detailed reasoning about why your way is better,
preferably with links to other sources. I change my mind often (for the better), so please convince me.

[open an issue]: https://github.com/ghengeveld/react-redux-styleguide/issues/new

## Table of Contents

- [ES6 and beyond](#es6-and-beyond)
  - [Variables: var, let and const](#variables-var-let-and-const)
  - [Variables: declaration and usage](#variables-declaration-and-usage)
  - [Arrow functions](#arrow-functions)
  - [Pure functions](#pure-functions)
  - [Classes](#classes)
- [React](#react)
  - [Components and containers](#components-and-containers)
  - [Dealing with props](#dealing-with-props)
- [Redux](#redux)
  - [Action creators](#action-creators)
  - [Reducers](#reducers)
  - [Connecting React components](#connecting-react-components)
- [Services](#services)
- [Utils](#utils)

## ES6 and beyond

Developers which have recently discovered the power of ES6 often feel compelled to use them on every occasion. This is
not a very good idea as it can quickly lead to unreadable code. With great power comes great responsibility.

### Variables: var, let and const

- Consider `var` deprecated. There is no use case for it anymore.
- Use `const` by default. Immutable bindings are a good starting point. Code is easier to read and comprehend because
  you can be sure the variable is never re-assigned.
- There's very few use cases for `let`. On many occasions you can use functions like map and reduce to avoid using it.

```js
// deprecated:
var foo = 'foo';

// avoid:
let bar = 'bar';

// avoid:
const foo = 'foo',
  bar = 'bar',
  baz = 'baz';

// recommended:
const foo = 'foo';
const bar = 'bar';
const baz = 'baz';
```

### Variables: declaration and usage

- Declare variables where you use them.
- Declare each variable as a full statement. It avoids the dangling comma discussion.
- Separate logical chunks of code with an empty line.

Common practice with `var` was to put all declarations at the top of their scope, even if they would only be used much
further down. The reason for this is that `var` declarations are automatically hoisted to the top of their scope, so
putting them there yourself makes the code more predictable. However, `let` and `const` are not hoisted. Therefore there
is no reason to 'hoist' them yourself. Generally it's better to declare variables just-in-time, right before the spot
where you are going to use them. This makes the code easier to follow. You won't have to go looking for usages before
they are defined, because they can't be used there. Finally, it makes it easier to extract a bunch of statements into a
function, which is a great way to improve readability even more.

Now that variables are declared where they are used, add some whitespace between them:

```
declare var1
declare var2
use var1, var2

declare var3
use var3

declare var4
use var4
```

### Arrow functions

- Consider anonymous functions deprecated. We have arrow functions for that now.
- Use arrow functions only as part of a larger (named) entity, not as a stand-alone entity.
- Declare functions after using them by leveraging function hoisting.

With the addition of the arrow function syntax, we have gained a very elegant and convenient way to define anonymous
functions. Because they use 'lexical this', usage of `this` is more predictable. Therefore you should use arrows instead
of regular anonymous functions.

Unfortunately, it's tempting to forgo regular functions entirely and switch to arrows completely. Developers seem to
think that with arrows, they never have to write that pesky `function` keyword again. However, a fully fledged function
definition still has its merits. Arrow functions have various benefits, but also two major drawbacks: they are anonymous
and they don't stand out in the code.

The most important thing when writing readable code is to name things. Naming your functions is the best way to document
what your code does. A well named function relieves the burden of having to read and comprehend the actual code. Another
drawback of anonymous functions is that they don't encourage reuse. With arrows it's too easy to write a new function
for each use case rather then reuse more generic functions. Finally, anonymous functions commonly aren't exported, which
makes them hard to unit test.

That being said, arrow functions certainly have their place. They are incredibly convenient as callback functions and
when combined with array methods (map, reduce, forEach, filter, etc.) or other functional constructs they make for very
elegant code. In the end it's up to you to find the right balance between elegance and documentation. Whenever you've
written an arrow function, take a step back and consider if using a named function instead would improve readability.

Like the Angular Style Guide we recommend the use of [function hoisting] in order to put your primary code at the top
and implementation details at the bottom. Such code is much easier to read because it's in chronologic order and focuses
on the bigger picture. If you've ever implemented this rule from the Angular Style Guide, you know this will let you
grok the contents of a file much faster.

You can of course assign your arrow function to a variable in order to 'name' it. Feel free to choose this approach if
it fits your coding style, but consider your less functional-oriented colleagues. However, this makes regular variables
and functions look extremely similar, making it harder to tell them apart. You will also lose the benefits of function
hoisting. However there are also use cases where this style makes more sense, particularly if the function is a
one-liner. Using a named function here would require at least three lines of code.

[function hoisting]: https://github.com/johnpapa/angular-styleguide#function-declarations-to-hide-implementation-details

### Pure functions

- Don't use shared mutable state or access variables from outer scope.
- Don't cause any side effects by running a function.
- Don't mutate any passed arguments, return a new value instead.

A sure way to reduce code complexity and enhance testability and readability is to keep your functions [pure]. This is
one of the primary aspects of functional programming. It involves writing functions in a purely input-output fashion.
Pure functions only work with the data they are given through their arguments and return some new data, without causing
side effects. That means they cannot access outside scope and they cannot mutate their arguments.

[pure]: https://en.wikipedia.org/wiki/Pure_function

### Classes

- Only use classes if you have a very good reason for it.
- Prefer composition over inheritance ("foo HAS a bar" instead of "foo IS a bar").
- Never inherit more than one level deep.
- Never use `instanceof` checks, use duck typing instead.

Classes have always been a hot topic of discussion in the JavaScript world. ES6 has introduced a new way to define
classes, even though there is a growing tendency to avoid them. Programmers coming from an OOP background will
appreciate them while the more functionally inclined don't like them. Developers working with React and Redux mostly
prefer the functional approach, which is why using classes is discouraged. Downsides include:

- Classes encourage mutation because they contain state.
- Classes encourage coupling because they are used as contracts.
- Classes encourage inheritance, but we want composition.
- Instances can't be easily cloned without losing their type, making it hard to write pure functions.
- Instance methods can't really be pure, it wouldn't make sense.
- Properties are never really private.

## React

### Components and containers

- Separate 'components' from 'containers'.
- Prefer using a function for components, a class for containers.
- Use PascalCase for component names.
- Put each component in its own directory, inside index.js.
- Expose components as the default export.
- Expose secondary functions as named exports for unit testing.
- Never export anonymous (arrow) functions.

React components come in two flavors: presentation components and container components, also known as [dumb vs smart
components]. We simply refer to them as 'components' and 'containers'.

React v0.14 introduced a new way to write components. In the past we would write every component as a class. However,
since presentation components are very lightweight, they don't need all the power that a class provides (i.e. lifecycle
methods and state).

The new way to write components uses a function instead of a class. Components defined that way are known as
'[functional stateless components][fsc]' because they cannot have any state and they are pure functions. This means they
will receive their props as an argument and must return the rendered component. It basically boils down to having only a
render method, which receives the props as an argument rather than accessing it as a class property. They also receive
context as a second argument.

Each component or container should be inside its own directory. This reduces the clutter when components use CSS and
other related files. By using `index.js` you won't have to change any import statements when you migrate from a file to
a folder with the same name. If you import a folder, `index.js` is inferred.

```js
// components/TodoItem/index.js
export default function TodoItem(props, context) {
  return <li onClick={() => context.onTodoClick()}>{props.label}</li>;
}

// containers/TodoList/index.js
export default class TodoList extends React.Component {
  render() {
    return <ul>{this.props.todos.map(todo => <TodoItem {...todo} />)}</ul>;
  }
}
```

[dump vs smart components]: https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0
[fsc]: https://facebook.github.io/react/blog/2015/10/07/react-v0.14.html#stateless-functional-components

### Dealing with props

- Start your render method with a destructuring assignment on props and/or context.
- Use the spread operator to assign multiple attributes in one go.

```js
const { label, completed } = this.props;
const { onTodoClick } = this.context;

return <TodoItem {...{ label, completed, onTodoClick }} />
```

## Redux

### Action creators

- Group related action creators in one file.
- Use [redux-promise] for asynchronous actions, or [redux-thunk] if you need more flexibility.
- Expose each action creator as a named export.
- Don't use a default export.

Action creators are functions which return an action object. An action object contains a type and optionally a payload
and metadata. Action objects should follow the [Flux Standard Action] schema.

```js
// actions/something.js
export function doSomething() {
  return {
    type: 'DO_SOMETHING',
    payload: { foo: 'bar' },
    meta: { baz: 'qux' }
  };
}
```

We could also use the createAction helper of redux-actions, but other than enforcing FSA it doesn't do much here in
terms of reducing boilerplate. In fact createAction is less readable and doesn't let us easily export named functions
(exporting anonymous or arrow functions should be avoided). Note that only `type` is mandatory.

['Ducks' is a proposal][ducks] to bundle action creators with their reducers into a single file. Such a bundle is called
a 'duck'. The goal is to move towards a modular application structure. So far the common practice is to group files by
type rather than by feature. A [similar transition][modular angularjs] has happened in the Angular world. This style
guide still follows the group-by-type pattern, but may move towards using ducks in the future.

[redux-promise]: https://github.com/acdlite/redux-promise
[redux-thunk]: https://github.com/gaearon/redux-thunk
[Flux Standard Action]: https://github.com/acdlite/flux-standard-action
[redux-actions]: https://github.com/acdlite/redux-actions
[ducks]: https://github.com/erikras/ducks-modular-redux
[modular angularjs]: https://medium.com/opinionated-angularjs/scalable-code-organization-in-angularjs-9f01b594bf06

### Reducers

A reducer handles incoming actions. All reducers are triggered for all actions, so it's up to each reducer to decide
whether or not to act on the incoming action. This is commonly done by switching on the action type. Reducers receive
a current state and an action object and must return a new (updated or not) state. Their function signature is as
follows:

```js
(state, action) => state
```

Reducers in Redux are pure functions, which means they cannot access outside information other than the arguments they
are given. It's also not allowed to mutate outside state, be it by referencing outer scope or by mutating objects which
they receive as arguments. In other words they must take the data they are given and return a new state without using
mutation. This can often be achieved by using the array and object spread operators.

The simplest way to create a reducer is to use the handleActions method of redux-actions. This avoids having to write a
switch statement and a default handler. Another benefit is that is enforces the use of FSA-compliant action objects. The
final reducer should be exposed as the default export. Individual handler functions should be exposed as named exports
in order to simplify unit testing.

```js
// using redux-actions

// reducers/something.js
import { handleActions } from 'redux-actions';
import { Example } from '../constants/actionTypes';

const initialState = {};

export default handleActions({
  [Example.doSomething]: doSomething
}, initialState);

export function doSomething(state, action) {
  return { ...state, value: action.payload.value };
}
```

### Connecting React components

- Always use a state selector function that returns the minimal state subset you need.
- Expose the connected component as default export.
- Expose the unconnected component as named export for unit testing.

```js
export class TodoList extends React.Component { ... }

const stateSelector = ({ todos, filters }) => ({ todos, filters });
export default connect(stateSelector)(TodoList);
```

The [reselect] library works well for more complex selectors and to achieve better performance.

[reselect]: https://github.com/rackt/reselect

## Services

- All methods should return a promise.
- Expose the entire service object as a default export.
- Expose Individual service methods as named exports for unit testing.
- Declare methods in alphabetical order.
- Declare helper functions underneath the methods, in chronological order.
- Never export anonymous (arrow) functions.

A service is where you commonly do AJAX requests to the back-end. All service methods should return a promise, even if
the result is calculated synchronously. This ensures a consistent and predictable API across all services and allows
changing to an asynchronous implementation later, without having to update service method calls all over the codebase.

Services should expose themselves via a default export to allow consumers to import the service as a whole. The reason
for this is that multiple services may expose the same generic method names, which can lead to name collisions. Services
should also expose each method individually using a named export in order to aid unit testing.

Service methods should be listed in alphabetical order. This keeps things tidy and avoids merge conflicts. Any helper
functions should be defined underneath all exported methods and listed in the order in which they are used (i.e.
chronological order, see 'Arrow functions'). Consider moving helper functions to utils and reusing them between
services.

```js
// services/SomethingService.js
export default {
  getSomething
};

export function getSomething() {
  return new Promise(resolve => resolve(helper()));
}

function helper() {
  return 'let me help you with that';
}
```

## Utils

- Group related util functions under a common name.
- List util functions in alphabetical order.
- Expose each function as named export.
- Don't use a default export.
- Util functions must be pure.
- Util functions should be reusable, but have a single purpose.

Utils is a collection of various supporting functions. It is a good idea to extract a function to a util when you are
doing the same thing in multiple places throughout the codebase. If you do are not using the function in several files,
it probably should not be a util function.

Exported functions should be defined in alphabetical order to make them easy to locate and avoid merge issues. Each
function should be exposed using a named export. A utils file should not expose a default export, as it does not
represent an entity as a whole but is merely a bag of related functions.

Utility functions may never keep internal state, nor can they use services. They should be pure functions. Each exported
function should be suitable for multiple uses (i.e. generic enough to be reused), but they should have a single well
defined purpose (i.e. do one thing, and do it well).

```js
// utils/serialization.js
export function upper(string) {
  return string.toLocaleUpperCase();
}
```
