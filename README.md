# JavaScript Core

1. Functions

1. Function Arguments

1. Lexical Scope

1. Closure

1. Hoisting

1. Context. What is this.


## Functions

Functions in JS are **High-Ordered**. Means that you can pass it as an argument to another function:

```js
doAction(function(){ console.log('done'); });
```

And return as the result:

```js
function add(a) {
  return function (b) {
    return a + b;
  }
}

var addToOne = add(1); // function
addToOne(2);

```

Initially JavaScript was designed in the functional paradigm that's why.

### Type of function creation:

You can create a functions in various ways in JS:

#### Function declaration

Declare it as a function:

```js
function foo() {}

var bar = foo;

bar();
```

#### Function expression

Assign it to a variable:

```js
var foo = function () {}
```

#### Using Function costructor

```js
var foo = new Function(['a', 'b'], 'return a+b'); // <- bad practice
```

## Arrow Functions notation

With ES6 (ES2015) there are few more features

```js
const add = (a, b) => a+b;
```

Is equivalent to:

```js
var add = function(a,b) {
  return a+b;
}.bind(this);
```

## Function Arguments

### Arguments number

Function can have unlimited number of arguments.
It does not restrict number or type of variables it's called with:

```js
function foo() {}
foo(1,2,3,4,5); // no error

function bar(a, b, c) {
  console.log(a, b, c);
}

bar(1); // 1 undefined undefined
```

### Arguments values

Arguments are passed as a value for primitives

string,
number,
boolean

### Reference to arguments

Each function has **arguments** constant that refers to *Array-like* object of arguments:

```js
function getArgs() {
  return arguments;
}

getArgs(1,2,3); // { '0': 1, '1': 2, '2': 3 }
```

It's NOT AN ARRAY, to make it so, there's a trick:

```js
[].slice.call(arguments)
```

## Lexical Scope

Functions see all variables were defined upper in parents:

```js

var a = 10;
console.log(a);
function foo() {
  console.log(a);
  return function () {
    var b = 20
    console.log(a, foo);
  }
}

console.log(b); // error

foo()(); // 10 10

```

Bot not from in children:

```js
function foo() {
  var a = 10;
}
console.log(a); // Error
```

## Closure

Thus, it saves all references to them which can cause unlikeful sideffects:

```js
for(var i = 0; i<5; i++) {
  setTimeout(function(){
    console.log(i);
  }, 0);
}
```

Resolve using **Self-Invoked** function:

```js
for(var i = 0; i<5; i++) {
  (function logInDelay(x) {
    setTimeout(function(){console.log(x);}, 0)
  }(i))
}
```

Or even better take it out from the loop, because creating functions in look or blocks is a bad practice.

```js
for(var i = 0; i<5; i++) {
  logInDelay(i);
}

function logInDelay(x) {
  setTimeout(function(){console.log(x);}, 0)
}
```

## Hoisting

Functions and variable declaration is being done in pre-compilation phase,
expressions and assignments follow in run-time. So you can invoke a function before it's literal declaration.

```js
var foo = undefined;
foo(); // Works fine
//bar(); // Error

function foo() {} // foo is declared as a function from start
var bar = function(){} // foo is declared as a var from start
```

## Function Context or What is "this"

Function has its context in which it was called and can be accessed with **this** keyword. Unlike static scope which cannot be changed, **this** context is dynamic and can be changed.

```js
var obj1 = {
  prop: 5,
  getProp: getProp
}

var obj2 = {
  prop: 10,
  getProp: getProp
}

getProp(); // undefined (this is window/global by default)
obj1.getProp(); // 5
obj1.getProp(); // 10
var foo = obj1.getProp;
foo(); // undefined (this is now referring to window)

function getProp () {
  return this.prop;
}

```

### Bind the context

You can capture the context of parent function in various ways.

- Bind to lexical scope:

```js
var obj = {
  foo: function() {
    var that = this;
    setTimeout(function() {
      console.log(that);
    });
  },
  hello: 'world'
}

console.log(obj.foo({hello: 'world'})); // { foo: [Function: foo], hello: 'world' }
```

- Using bind, call or apply

```js
function parent() {
  setTimeout(function() {
    console.log(this);
  }.bind(this));
}

console.log(parent.call({hello: 'world'})); // { hello: 'world' }
```

- Using arrow functions

```js
function parent() {
  setTimeout(() => console.log(this), 0);
}

console.log(parent.call({hello: 'world'})); // { hello: 'world' }
```

### Constructor's context

If the function is called with the **new** operator, it's **this** will be referring to an instance
was created:

```js
function Foo() {
  console.log(this);
}

Foo(); // window object
new Foo(); // Foo {}
```

All global JS variables are accessed in window object:

```js
var hello = 'Hello World';

function sayHello() {
  console.log(this.hello);
}

sayHello();
```

And so, some critical global things can be damaged with mutating **this** within function is called in global context.

So:

1. Don't use global variables
1. Use **call**, **apply** and **bind** to capture the context
1. Use arrow (**=>**) functions for callbacks
1. Go immutable as much as possible
1. Use contextless functions as much as possible

## Prototypes

Prototype is an elegant lightweight way to share methods among objects without changing
their own properties and perform inheritance between objects.

You can add prototype in various ways.

### Classic way: Function constructor

```js
function Cat() {}
Cat.prototype.say = function () { console.log('Meow'); };

var tom = new Cat();
tom.say(); // Meow
```

### ES5 way: Object.create

```js
var catProto = {say: function () { console.log('Meow'); }}

var tom = Object.create(catProto);
tom.say(); // Meow
```

### ES6 way: Classes

```js
class Cat {
  say() { console.log('Meow'); }
}

var tom = new Cat();
tom.say(); // Meow
```

The right way to get object's prototype is by using ```Object.getPrototypeOf(tom)```;

## Prototype-driven Inheritance

### Classical

```js
function Animal() {}
Animal.prototype.say = function() {console.log(this.hello)}

function Cat() {
  Animal.call(this); // call super constructor
  this.hello = 'Meow!';
}

Cat.prototype = new Animal();
```

### Same with ES/next classes

```js
class Animal {
  say() { console.log(this.hello); }
}

class Cat extends Animal {
  hello = 'Meow!'; // ES7-like property set
}
```

### Pure functional, with no classes

```js
var cat = Object.create(animalProto, {
  hello: {value: 'Meow'}
});
```

## Reference

1. [MDN JavaScript](https://developer.mozilla.org/en/docs/Web/JavaScript)

1. [JavaScript: The Good Parts, Douglas Crockford](http://bdcampbell.net/javascript/book/javascript_the_good_parts.pdf)

1. [JavaScript: The Good Parts (Video)](https://www.youtube.com/watch?v=hQVTIJBZook)

1. [Anjana Vakil: Learning Functional Programming with JavaScript - JSUnconf 2016](https://www.youtube.com/watch?v=e-5obm1G_FY)

1. [Современный учебник Javascript](https://learn.javascript.ru/)

1. [Browser Rendering Optimization](https://classroom.udacity.com/courses/ud860)
