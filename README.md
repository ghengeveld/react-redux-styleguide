# React / Redux Style Guide

This is an opinionated style guide for developing applications in ES6+ with React and/or Redux.

These styles are based on current best practices in the React and Redux communities, as well as real life experience
with these tools in the field. It puts great focus on writing readable and maintainable code and using new JavaScript
features in a responsible manner.

## Credit

Much of this style guide is based on the Redux documentation and ideas from [Dan Abramov](https://github.com/gaearon)
in particular. Furthermore, it was inspired by the [Angular Style Guide](https://github.com/johnpapa/angular-styleguide)
by John Papa.

## ES6 and beyond

Developers which have recently discovered the power of ES6 often feel compelled to use them on every occasion. This is
not a very good idea as it can quickly lead to unreadable code. With great power comes great responsibility.

### Variables: var, let and const

- Consider `var` deprecated. There is no use case for it anymore.
- Use `const` by default. Immutable bindings are a good starting point. Code is easier to read and comprehend because
  you can be sure the variable is never re-assigned.
- There's very few use cases for `let`. On many occasions you can use functions like map and reduce to avoid using it.

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

- Prefer named functions and name them properly.
- Use arrow functions only as part of a larger entity, never as a stand-alone entity.
- Declare functions after using them by leveraging function hoisting.

The most abused feature of ES6 is the arrow function syntax. Developers seem to think that with arrows, they never have
to write that pesky `function` keyword again. Unfortunately for them, a fully fledged function definition still has its
merits. Arrow functions have two major drawbacks: they are anonymous and they don't stand out in the code.

The most important thing when writing readable code is to name things. Naming your functions is the best way to document
what your code does. A well named function releases the burden of having to read and comprehend the actual code. Another
drawback of anonymous functions are that they don't encourage reuse. With arrows it's too easy to write a new function
for each use case rather then reuse more generic functions. Finally, anonymous functions commonly aren't exported, which
makes them hard to unit test.

You can of course assign your arrow function to a variable in order to 'name' it. Feel free to choose this approach if
it fits your coding style. However, this makes regular variables and functions look extremely similar, making it harder
to tell them apart. The benefit of function hoisting is also lost.

Like the Angular Style Guide we recommend the use of function
[hoisting](https://github.com/johnpapa/angular-styleguide#function-declarations-to-hide-implementation-details) in order
to put your primary code at the top and implementation details at the bottom. Such code is much easier to read because
it's in chronologic order and focuses on the bigger picture.

### Pure functions

- Don't use shared mutable state or access variables from outer scope.
- Don't cause any side effects by running a function.
- Don't mutate any passed arguments, return a new value instead.

A sure way to reduce code complexity and enhance testability and readability is to write your functions pure. This is
one of the primary aspects of functional programming. It involves writing functions in a purely input-output fashion.
Pure functions only work with the data they are given through their arguments and return some new data, without causing
side effects. That means they cannot access outside scope and they cannot mutate their arguments.

### Classes

- Only use classes if you have a very good reason for it.
- Prefer composition of inheritance.
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

### Components

- Separate 'components' from 'containers'.
- Use a function for components, a class for containers.
- Put each component in its own directory, inside index.js.
- Expose components as the default export.
- Expose secondary functions as named exports for unit testing.
- Never export anonymous (arrow) functions.

React components come in two flavors: presentation components and container components, also known as dumb vs smart
components. We simply refer to them as 'components' and 'containers'.

React v0.14 introduced a new way to write components. In the past we would write every component as a class. However,
since presentation components are very lightweight, they don't need all the power that a class provides (i.e. lifecycle
methods and state).

The new way to write components uses a function instead of a class. Components defined that way are known as 'functional
stateless components' because they cannot have any state and they are pure functions. This means they will receive their
props as an argument and must return the rendered component. It basically boils down to having only a render method,
which receives the props as an argument rather than accessing it as a class property. They also receive context as a
second argument.

Each component or container should be inside its own directory. This reduces the clutter when components use CSS and
other related files. By using `index.js` you won't have to change any import statements when you migrate from a file to
a folder with the same name. If you import a folder, `index.js` is inferred.

#### Recommended

```js
// components/MyComponent/index.js
export default function MyComponent(props, context) {
  return <div {...context} {...props} />;
}

// containers/MyContainer/index.js
export default class MyContainer extends React.Component {
  render() {
    return <div {...this.context} {...this.props} />;
  }
}
```

## Redux

### Action creators

Action creators are functions which return an action object. An action object contains a type and optionally a payload
and metadata. Action objects should follow the [Flux Standard Action](https://github.com/acdlite/flux-standard-action)
schema.

#### Recommended

```js
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
(exporting anonymous or arrow functions should be avoided).

Note that only `type` is mandatory. It should be an actionType constant.

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

#### Recommended (using redux-actions)

```js
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

## Services

- Always return a promise.
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

#### Recommended

```js
export default {
  getSomething
};

export function getSomething() {
  return new Promise(resolve => resolve('something'));
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

Utility functions should be defined in alphabetical order. Each function should be exposed using a named export. A utils
file should not expose a default export, as it does not represent an entity as a whole but is merely a bag of related
functions.

Utility functions may never keep internal state, nor can they use services. They should be pure functions. Each exported
function should be suitable for multiple uses (i.e. generic enough to be reused), but they should have a single well
defined purpose (i.e. do one thing, and do it well).
