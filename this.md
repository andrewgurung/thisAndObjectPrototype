## this
- `this` is a binding made when a function is invoked
- What `this` refers to is entirely based by the call-site where the function is called

### False Assumption for `this`
- `this` refers to the function itself


### Consider: Explicitly passing object reference (context) around
```js
function greet(context) {
  console.log( "Hi " + context.name );
}

var foo = {
  name: "Foo"
};

var bar = {
  name: "Bar"
};

greet( foo ); // Hi Foo
greet( bar ); // Hi Bar

```

### Elegant way of passing object reference implicitly using `this`
```js
function greet() {
  console.log("Hi " + this.name );
}

var foo = {
  name: "Foo"
};

var bar = {
  name: "Bar"
};

greet.call( foo ); // Hi Foo
greet.call( bar ); // Hi Bar
```

### Function as objects
- All functions in Javascript are objects
- Like objects, we can add properties to functions

Consider:

```js
function greet(name) {
  console.log( "Hi " + name );
}

greet.age = 30; // Adding 'age' property to `greet` function

greet( "Foo" ); // Hi Foo
console.log( greet.age ); // 30
```

## Accessing function property by the function itself

```js
function greet(name) {
  console.log( "Hi " + name );
  console.log( "Age: " + this.age ); // Try to access its own function property `age`
}

greet.age = 30; // Adding 'age' property to `greet` function

var age = 100;

greet( "Foo" ); // Hi Foo
                // Age: 100
```

### Problem:
- `this` doesn't refer to `greet` function
- `this` actually refers to global object since the call-site `greet( "Foo" )` was invoked from the lexical scope of global object
- But we want `this` to point to `greet` function and print `this.age` as 30 not 100

### Solution #1: Define a property in the reach of its lexical scope

```js
function greet(name) {
  console.log( "Hi " + name );
  console.log( "Age: " + data.age ); // Try to find `data` object in its lexical scope
}

var data = {
  age: 30 // Adding 'age' property to `data` object in same lexical scope as greet (Global Scope)

}

var age = 100;

greet( "Foo" ); // Hi Foo
                // Age: 30
```

### Solution #2: Use the function identifier

```js
function greet(name) {
  console.log( "Hi " + name );
  console.log( "Age: " + greet.age ); // Try to access its own function property `age` through `greet` function identifier
}

greet.age = 30; // Adding 'age' property to `greet` function

var age = 100;

greet( "Foo" ); // Hi Foo
                // Age: 30
```

Note:
- It is easier to refer a named function instead of anonymous function
- Best Practice: Always use named function expression
- Deprecated: `arguments.callee` can be used to refer to the function itself

### Best Solution #3: Force `this` to point to the function object itself

- Use `call( objectReference, optional_Param )` method to invoke `greet` function
- Pass `greet` function as the objectReference

```js
function greet(name) {
  console.log( "Hi " + name );
  console.log( "Age: " + this.age ); // Try to access its own function property `age` through `greet` function identifier
}

greet.age = 30; // Adding 'age' property to `greet` function

var age = 100;

greet.call( greet , "Foo" ); // Hi Foo
                             // Age: 30
```

## Call-Site Rules
- Call-site refers to the location where a function is called
- `this` binding is based purely on call-site rules

### Rule #1: Default Binding
- When function is called as standalone function with plain, undecorated function reference
- When no other rules apply, we fall back to default binding
- In strict mode, global object is not eligible for default binding
- `this` = global object

```js
function foo() {
  console.log( this.a );
}

function bar() {
  "use strict"
  console.log( this. a);
}
var a =  2;

foo(); // 2
bar(); // undefined
```

### Rule #2: Implicit Binding
- When function is invoked in context of an object
- Context object owns or contains the function
- `this` = context object

```js
function foo() {
  console.log( this.a );
}

var obj = {
  a: 2,
  foo: foo
};

obj.foo(); // 2
```

**Losing Implicit Binding #1: Function used as a new reference**
- Instead of invoking function with context object, we use it as a reference to create another variable, later called in a different call-site
- Called as a plain undecorated call

```js
function foo() {
  console.log( this.a );
}

var obj = {
  a: 2,
  foo: foo
};

var a = "Global Object";
var bar = obj.foo; // Just a reference to obj.foo() function

// Plain call would be treated as Default binding
bar(); // Global Object
```

**Losing Implicit Binding #2: Function passed as a reference in call back**
- Parameter passing is just an implicit reference assignment

```js
function foo() {
  console.log( this.a );
}

var obj = {
  a: 2,
  foo: foo
};

var a = "Global Object";

setTimeout( obj.foo, 1000); // Global Object
```

***setTimeout Internal Pseudo Implementation***
```js
function setTimeout(fn, delay) {
  // wait (somehow) for `delay` milliseconds
  fn(); // <-- call-site! fn is just a reference
}
```

### Rule #: Explicit Binding

### Rule #: new Binding
