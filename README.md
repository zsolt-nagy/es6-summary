# es6-summary

A short summary of ES6 features and their ES5 equivalents.


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
    let args = arguments;
    statements;
}.bind( this );
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
