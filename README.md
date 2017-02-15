# es6-summary

A short summary of ES6 features and their ES5 equivalents. 

Access in-depth [ES6 articles](http://www.zsoltnagy.eu/category/es6/) here.

**Table of contents**

1. [Arrow Functions](#arrow-functions)
2. [Functions](#functions)
3. [Scope](#scope)
4. [Default Argument Values](#default-argument-values)
5. [Classes](#classes)
6. [Object Property Shorthand Notation](#object-property-shorthand-notation)
7. [Destructuring](#destructuring)
8. [Rest Parameters](#rest-parameters)
9. [Spread Operator](#spread-operator)
9. [Objects](#objects-1)
10. [Tail call optimization](#tail-call-optimization)
11. [Symbols](#symbols)
12. [Sets](#sets)
13. [Maps](#maps)
14. [Weak Sets](#weak-sets)
15. [Weak Maps](#weak-maps)
16. [for...of loop](#forof-loop)
17. [Iterable Interface](#iterable-interface)
18. [Generator functions](#generators-functions)
19. [Strings](#strings)
20. [Template literals](#template-literals)
21. [Tag functions for Template Literals](#tag-functions-for-template-literals)
22. [Promises](#promises)
23. [Modules](#modules)
24. [Reflect API](#reflect-api)
25. [Proxies](#proxies)
26. [59 Exercises](#59-exercises)

## Arrow Functions

- describes a mapping between the argument list and return value
- the return value may be in a block (see `2`, `3`, and `4`)
- only use it when implicit context binding is acceptable 
- don't use arrow functions in classes, constructors, prototype extensions

```
// 1.
a => value;

// 2.
a => { return value; }

// 3.
( a, b ) => { 
    statements;
    return value;
}

// 4.
( ...args ) => {
    statements;
    return value;
}
```

```
// 1.
// 2.
function( a ) { return value; }.bind( this );

// 3.
function( a, b ) {
    statements;
}.bind( this );

// 4.
function() {
    var args = arguments;
    statements;
}.bind( this );
```

## Functions

### Name property

```
const f = () => null;

f.name
> "f"
```

### `new.target`

```
const C = function() {
    console.log( new.target === C );
}

const o = new C();
> true
```

## Scope

- `var`: function scope

```
(function() {
    // first and second are accessible
    // first = undefined, second = undefined
    var first = 1;
    {
        var second = 2;
    } 
    // first and second are accessible
    // first = 1, second = 2
});
```

After hoisting:

```
(function() {
    var first, second;
    first = 1;
    {
        second = 2;
    } 
    // first and second are accessible
    // first = 1, second = 2
});
```


- `let`, `const`: block scope

`let` and `const` declarations are hoisted just like vars. However, their values are not accessible before their first assignment.

```
{
    // accessing radius or PI throws an error
    const PI = 3.14;
    let radius = 2;
    // radius and PI are accessible
    // radius may be modified
}
// accessing radius or PI throws an error
```

## Default argument values

**ES6:**

```
function( name, phone = '-' ) {
    // ...
}
```

**ES5:**

```
function( name, phone ) {
    phone = typeof phone === 'undefined' ? '-' : phone;
    // ...
}
```

*Warning*: default arguments does not affect the `arguments` array

```
const f = ( x = 0, y = 0 ) => {
    console.log( arguments.length );
};

f( 0 )
> 1
```

## Classes

### Basics

**ES6**:

- *concise method syntax* has to be used

```
class Shape {
    constructor( color ) {
        this.color = color;
    }
    getColor() {
        return this.color;
    }
}
```

**ES5**:

```
function Shape( color ) {
    this.color = color;
}

Shape.prototype.getColor = function() {*
    return this.color;
}
```

### Inheritance

- `Rectangle` is the child class, `Shape` is the parent class
- redefining a method with the same name in the child class *shadows* the method in the parent class
- `super` has to be called in the constructor in the child class
- in the absence of a constructor, a constructor including a `super` call is implicitly assumed

**ES6:**

```
class Rectangle extends Shape {
    constructor( color, width, height ) {
        super( color );
        this.width = width;
        this.height = height;
    }
}
```

**ES5:**

```
function Rectangle( color, width, height ) {
    Shape.call( this, color );
    this.width = width;
    this.height = height;
}

Rectangle.prototype = Object.create( Shape.prototype ); 
Rectangle.prototype.constructor = Rectangle;
```

### Getters and Setters

```
class Rectangle extends Shape {
    constructor( color, width, height ) {
        super( color );
        this.width = width;
        this.height = height;
    }
    get area() {
        return this.width * this.height;
    }
    set area( value ) {
        console.log( 'Attempting to set area to ' + value );
        throw 'Setting area is not allowed';
    }
}

const myRectangle = new Rectangle( 'red', 5, 3 );

myRectangle.area
> 15

myRectangle.area = 25
> Attempting to set area to 25
```

### Static methods

- class level methods
- cannot be accessed from instances

```
class Die {
    static cast() { return Math.round( Math.random() * 6 ) + 1; }
}

Die.cast()
> 3
```

## Object property shorthand notation

**ES6:**

```
var language = 'Markdown';
var extension = 'md';

var file = {
    language,
    extension
};
```

**ES5**:

```
var language = 'Markdown';
var extension = 'md';

var file = {
    language: language,
    extension: extension
};
```

## Destructuring

- `null` or `undefined` on the right hand side of a destructuring expression yields a JavaScript error

### Arrays

**ES6:**

```
var a = 1, b = 2;

// 1.
[a, b] = [b, a + b];

// 2.
var [,c] = [a, b];

// 3. 
var [,,d] = [1, 2];
```

**ES5:**

```
var a = 1, b = 2;

// 1.
var temp_a = a, temp_b = b;
a = temp_b;
b = temp_a + temp_b;

// 2.
var temp_b2 = b;
var c = temp_b2;

// 3.
var d = undefined;
```

### Objects

**ES6:**

```
var file = {
    filename: 'es6.md',
    metadata: {
        language: 'Markdown',
        extension: 'md'
    }
};

var { filename, metadata: { language } } = file;
// same as:
// var { filename, metadata: { language: language } } = file;
```

**ES5:**

```
var file = {
    filename: 'es6.md',
    metadata: {
        language: 'Markdown',
        extension: 'md'
    }
};

var temp_filename = file.filename;
var temp_language = file.metadata.language;
var filename = temp_filename;
var language = temp_language;
```

### Return value of a destructuring expression

- `LEFT = RIGHT` returns `RIGHT`

```
{ a } = { b } = { a: 1, b: 2 }   

// is evaluated as:
// b becomes 2
{ a } = { a: 1, b: 2 }

// a becomes 1
{ a: 1, b: 2 }
```

### Destructuring function arguments

```
function f( L1, L2 ) { ... }

f( R1, R2 );
```

executes

```
L1 = R1;
L2 = R2;
```

## Rest parameters

**ES6**:

```
( function( first, ...rest ) {
    console.log( 'first:', first );
    console.log( 'rest:', rest );
} )( 1, 'Second', 3 );
> first: 1
> rest: ['Second', 3]
```

**ES5**:

- note: `arguments` is not an array in JavaScript, it has to be converted to an array with `Array.from` to be able to `slice` it

```
( function() {
    var first = arguments[0];
    var rest = Array.from( arguments ).slice( 1 );
    console.log( 'first:', first );
    console.log( 'rest:', rest );
} )( 1, 'Second', 3 );
> first: 1
> rest: ["Second", 3]
```

## Spread operator

```
const arr = [1, 2, 3];

// ...arr spreads the elements of arr into comma separated values
[ ...arr, ...arr ]
> [1, 2, 3, 1, 2, 3]

Math.max( ...arr ) // same as Math.max( 1, 2, 3 )
> 3
```

- Strings are spread as comma separated strings of one character each.

```
[...'ES6']
> ["E", "S", "6"]
```

- ES5 equivalents: loops, utility functions, `call` / `apply` function for calling functions with a variable number of arguments

## Objects

### Object.assign

**ES6:**

```
Object.assign( target, ...sourceList )
```

- copies all key-value pairs of `sourceList` to `target`, mutating `target`
- copying is done from left to right
- returns target

Example:

```
var target = { a: 1 };
Object.assign( target, { b: 1}, { a: 2, b: 2 } );
// target becomes { a: 2, b: 2} AND returns target
```

**ES5:**

- assume all arguments are objects, and they are not null
- in practice, error handling should be taken care of, this implementation is for illustration purposes only

```
Object.assign = function( target /*, ...sourceList */ ) {
    var source;
    for ( var i = 1; i < arguments.length; ++i ) {
        source = arguments[i]; 
        for ( var key in source ) {
            if ( source.hasOwnProperty( key ) ) target[key] = source[key]; 
        }
    }
```

### Computed object keys

- Place any JavaScript expression as an object key between `[` and `]`
- The JavaScript expression is stringified and converted into an object key

```
{
    [ 3 + 2]: 1,
    [ [] ]: 2
}
```

becomes

```
{
    "5": 1,
    "[object Object]": 2
}
```

### Shorthand method notation for objects

```
const publicInterface = {
    operation() { return 'ES6'; }
}
```

### Object prototype extensions

```
Object.getPrototypeOf( o );
Object.setPrototypeOf( o, newProto );
```

## Tail call optimization

- Tail call optimization avoids using the stack for a function call
- Function calls in tail position are tail-call optimized
- A function is in tail position if it's the last action in the function 
- Compatibility: currently low support ( https://kangax.github.io/compat-table/es6/ )

Regular recursion:

```
function sumToN( n ) { 
    if ( n <= 1 ) return n;
    return n + sumToN( n - 1 ); 
};
```

Tail call optimization with *accumulator variables*:

```
function sumToN( n, sum = 0 ) { 
    if ( n <= 1 ) return sum; 
    let result = sum + n; 
    return sumToN( n - 1, result ); 
};
```

## Symbols

- `Symbol()` creates a unique symbol
- Objects can have string or symbol keys
- Node.js does not print Symbol keys
- `JSON.stringify` excludes Symbol keys
- `Object.assign` copies Symbol keys over to `target`

```
const s1 = Symbol();
const s2 = Symbol();

s1 === s2 
> false

typeof s1
> "symbol"

const o = {
    [s2]: 1,
    [Symbol()]: 2
};

o[s1] = 3;

JSON.stringify( o )
> "{}"
```

### Global Symbol Registry

```
const s1 = Symbol.for( 'key' );
const s2 = Symbol.for( 'key' );

s1 === s2
> true

Symbol.keyFor( s1 ) 
> "key"
```

## Sets

- arbitrary values
- values can be enumerated using the *iterable interface*

```
let colors = new Set();
colors.add( 'red' );
colors.add( 'green' );

colors.size
> 2

colors.has( 'green' )
> true

colors.delete( 'green' )
> true
```

## Maps

- arbitrary keys
- arbitrary values
- keys can be enumerated using the *iterable interface*

```
let horses = new Map();
horses.set( 8, 'Chocolate' );

horses.size
> 1

horses.has( 8 )
> true

horses.get( 8 )
> 'Chocolate'

horses.delete( 8 )
> true
```

Alternative map constructor

```
let horses = new Map( [[8, 'Chocolate'][3, 'Filippone]] );
```

## Weak sets

- sets hold weak reference to their values: if the only reference to a value is in the set, the value is garbage collected
- size of the set is unknown
- weak sets may only store objects
- weak sets are not iterable

```
let firstElement = { order: 1 }, secondElement = { order: 2 }; 
let ws = new WeakSet( [ firstElement, secondElement ] );

ws.has( firstElement ) 
> true

firstElement = {};
ws.has( firstElement ) 
> false
```

## Weak maps

- object keys, arbitrary values
- only the keys of the weak map are weak: in case the map holds the only reference to its key, the key is garbage collected

```
let firstElement = { order: 1 }, secondElement = { order: 2 }; 
let wm = new WeakMap();

wm.set( firstElement, 1 ); 
wm.set( secondElement, {} );

wm.get( secondElement ) 
> {}

secondElement = {};
wm.get( secondElement ) 
> undefined
```

## `for...of` loop

- works on *iterables*

```
const arr = [1, 2, 3];
const message = 'hello';

for ( let i of arr ) console.log( i );
> 1
> 2
> 3

for ( let ch of message ) {
    console.log( ch );
}
> 'h'
> 'e'
> 'l'
> 'l'
> 'o'

let colors = new Set();
colors.add( 'red' );
colors.add( 'green' );
for ( let color of colors ) console.log( color );
> 'red'
> 'green'

let horses = new Map( [[8, 'Chocolate'][3, 'Filippone]] );
for ( let [key, value] of horses ) console.log( key, value );
> 8 'Chocolate'
> 3 'Filippone'
```

## Iterable interface

### Properties of iterables and iterators

- *Iterable object*: has a `[Symbol.iterator]` method returning an *iterator object*
- *Iterator object*: have a `next()` method returning `{ done: <Boolean>, value: <NextValue> }` of the iteration 
- when `done` is truthy, the iteration ends
- when `done` is falsy, `value` is the upcoming element of the iteration

### Consumer constructs

- `for...of` loop consumes iterables
- `...` (spread) operator consumes iterables

### Built-in iterables:

- Arrays
- Strings
- DOM data structures
- Maps
- Sets

Example: 

```
let colors = new Set( [ 'red', 'yellow', 'green' ] ); 
let horses = new Map( [ [5, 'QuickBucks'], [8, 'Chocolate'], [3, 'Filippone'] ] );

console.log( colors.entries() ); 
> SetIterator {["red", "red"], ["yellow", "yellow"], ["green", "green"]}

console.log( colors.keys() ); 
> SetIterator {"red", "yellow", "green"}

console.log( colors.values() ); 
> SetIterator {"red", "yellow", "green"}

console.log( horses.entries() );
> MapIterator {[5, "QuickBucks"], [8, "Chocolate"], [3, "Filippone"]}

console.log( horses.keys() ); 
> MapIterator {5, 8, 3}

console.log( horses.values() ); 
> MapIterator {"QuickBucks", "Chocolate", "Filippone"}
```

## Generators functions

- Return an iterable that is also an iterator
- `function*` keyword creates a generator function
- `yield` keyword yields the next value returned by the iterator, with `done: false`
- `return v` exits the generator with `{ done: true, value: v }`. `v` is not consumed by data consumers

```
function *getLampIterator() { 
    yield 'red'; 
    yield 'green'; 
    return 'lastValue'; 
}

let lampIterator = getLampIterator();
console.log( lampIterator.next() ); 
> Object {value: "red", done: false}

console.log( lampIterator.next() ); 
> Object {value: "green", done: false}

console.log( lampIterator.next() ); 
> Object {value: "lastValue", done: true}
```

### Combining generators

```
let generator1 = function *() { ... };
let generator2 = function *() { ... };

let combinedGenerator = function *() {
    yield *generator1();
    yield *generator2();
}
```

### Passing parameters to iterables

```
let greetings = function *() { 
    let name = yield 'Hi!'; 
    yield `Hello, ${ name }!`; 
}

let greetingIterator = greetings();

console.log( greetingIterator.next() ); 
> Object {value: "Hi!", done: false}

console.log( greetingIterator.next( 'Lewis' ) ); 
> Object {value: "Hello, Lewis!", done: false}
```

## Strings

```
const s = 'hello'

s.startsWith( 'he' );
> true

s.endsWith( 'lo' );
> true

s.includes( 'll' );
> true

s.repeat( 3 );
> 'hellohellohello'

// codePointAt returns the character code regardless of whether it's a 2, 3, or 4 byte character
'\u{1f600}\u{1f600}'.codePointAt( 0 );
> 128512
'\u{1f600}\u{1f600}'.codePointAt( 1 ); // garbage: second half of the first character
> 56832
'\u{1f600}\u{1f600}'.codePointAt( 2 );
> 128512
```

## Template literals

- evaluate JavaScript expressions inside `${` and `}`
- can span multiple lines
- return a string

```
const x = '3 + 2';

console.log( `${x} is ${3 + 2}
.
.
.` );
> 3 + 2 is 5
> .
> .
> .
```

## Tag functions for template literals

```
tagFunction( literalFragmentArray, ...expressionSubstitutions ) {
    // returns string returned by the template literal
}
```

Example:

```
const toUpperExpressionsTag = ( literals, ...expressions ) => {
    let str = '';
    for ( let i = 0; i < expressions.length; ++i ) {
        str += literals[i] + expressions[i].toUpperCase();
    }
    str += literals[ literals.length - 1];
    return str;
}
const dave = 'dave';

toUpperExpressionsTag`Hello ${ dave }!`
> "Hello DAVE!"
```

## Promises

- promises are immutable
- they are either kept or broken
- when a promise is kept, we are guaranteed to receive a value
- when a promise is broken, we are guaranteed to receive a reason (error)

### Promise states

- `pending`: may transition to `fulfilled` or `rejected`
- `fulfilled`: must have a value
- `rejected`: must have a reason for rejection

### Creating promises

```
let promise = new Promise( function( resolve, reject ) { 
    // call function resolve( value ) to resolve a promise 
    // call function reject( reason ) to reject a promise 
} );
let onFulfilled = function() { /* success handler */ }
let onRejected = function() { /* rejection handler */ }

promise.then( onFulfilled, onRejected );
```

Then is chainable: it returns a promise

```
promise.then( s1, r1 ).then( s2, r2 );
```

- On success, it returns `s2( s1( v1 ) )` in case `promise` is resolved with `v1` 
- If `promise` is resolved with return value `v1`, and `s1( v1 )` returns a value, then the promise calling `s2` will be fulfilled with value `s1( v1 )`

Errors can be caught with `promise.catch()`

```
promise.then( onFulfilled1 ).then( onFulfilled2 ).catch( onReject );
```

### Creating automatically resolved promises

```
let promise = Promise.resolve( v );
```

### Example

```
p.then( ( value ) => console.log( 'Value:', value ) ) 
 .then( () => { throw new Error('Error in second handler' ) } ) 
 .catch( ( error ) => console.log( 'Error: ', error.toString() ) );
```

### `Promise.all`

- handles an *iterable* of promises
- `onFulfilled` is called once *all* promises in the iterable are resolved
- `onRejected` is called once *any* promise in the iterable is rejected

```
Promise.all( [ promise1, promise2 ] ).then( onFulfilled, onRejected );
```

## Modules

- Importing module from `./moduleName.js` and `./folder/moduleName2.js`

```
import module from 'moduleName';
import { key1, key2 } from 'folder/moduleName2';  // notice the destructuring on the left
```

- Exporting objects in `./moduleName.js`:

```
class C { ... }

export default C;
```

## Reflect API

The Reflect API is far too complex to be summarized in a couple of paragraphs. It is also an advanced topic, requiring in-depth understanding. For this reason, I highly recommend the below article.

[Reflect API](http://www.zsoltnagy.eu/the-reflect-api-of-es6/)

## Proxies 

Proxies are not only complex, but they heavily build on the Reflect API. Check out this article to learn them.

[Proxies in Practice](http://www.zsoltnagy.eu/es6-proxies-in-practice/)

## 59 Exercises

Check out [ES6 in Practice](https://leanpub.com/es6-in-practice), and put theory into practice by solving 59 exercises, and checking out their reference solutions.
