## [[Prototype]]
- Objects in Javascript have an internal property `[[Prototype]]`, which is simply a reference to another object
- In most cases `[[Prototype]]` is a non-null value
- Top-end of every normal `[[Prototype]]` chain is the built-in `Object.prototype`
- `Object.prototype` object includes common utilities such as `.hasOwnProperty(..)`, `toString()`, `valueOf()` etc

Consider:
```js
var myObject = {
  a: 2
};

myObject.a; // 2

// create an object linked to `anotherObject`
var anotherObject = Object.create( myObject );

anotherObject.a; // 2

anotherObject.c; // undefined

for (var k in anotherObject) {
  console.log( "Property: " + k );
}
// Property: a

("a" in anotherObject); // true
```

- When you reference a property using `myObject.a`
  - First it will invoke `[[Get]]` operation to check if the object itself has the property `a`
      - Eg: `myObject.a` will invoke `[[Get]]` operation and finds `a` property
  - If not, `[[Get]]` operation will follow the `[[Prototype]]` link of the object  
      - Eg: `Object.create( myObject )` will create a new object that has `[[Prototype]]` property linked to the specified object `myObject`  
      - `anotherObject.a` will not find `a` property using `[[Get]]` operation
      - It looks at its `[[Prototype]]` which happens to be `myObject`
  - This process continues until either a matching property name is found, or the `[[Prototype]]` chain ends
  - If no matching property is ever found, [[Get]] operation returns `undefined`
    - Eg: `anotherObject.c` returns `undefined`
- `for..in` loop iterates over its enumerable properties including the `[[Prototype]]` chain
  - Eg: for (var k in anotherObject)
- But `in` operator will check for existence of a property in the entire chain of object (regardless of enumerability)
  - Eg: ("a" in anotherObject);

## Shadowing
- If a property name appears on both the object itself and at higher of the [[Prototype]] chain, the property directly on object shadows any property which appears in the chain
- Best Practice: Avoid shadowing if possible

#### Rules: (When property is `not` already on the object itself)
1. Property (normal data accessor) found on higher [[Prototype]] chain and it's **not** marked as read-only `(writable:true)`  
   **Result:** Add property directly to the object itself resulting in `shadowed property`
2. Property (normal data accessor) found on higher [[Prototype]] chain but it's marked as read-only `(writable:false)`  
   **Result:** No shadowing occurs. (Running in strict mode will throw an error)
3. Property found on higher [[Prototype]] chain but it's a setter
   **Result:** No shadowing occurs. But setter will always be called

#### Note: If you want to shadow a property in case #2 and #3
- Use `Object.defineProperty(..)`

Consider

```js
var myObject = {
  a: 2
};

var anotherObject = Object.create( myObject );
myObject.a; // 2
anotherObject.a; // 2

// Shadowing of myObject.a
anotherObject.a = 5;
anotherObject.a; // 5
```

## Class
- Javascript doesn't have abstract pattern/blueprint called `Class` to create objects
- JS is one unique language where objects can be created directly
- By default all functions get a public, non-enumerable property called `prototype`
- Unlike traditional class-oriented languages, JS doesn't perform `copy operation` (make copies from one object/class to another instance)
- `new Function_Name()` and `Object.create(..)` results in a new object
  - This new object is internally [[Prototype]] linked to the `Function_Name.prototype` object  
  - End up with two objects linked to each other
- `Inheritance`, `Prototypal Inheritance` and `Differential Inheritance` are wrong terms to explain how Javascript works

  Consider:
  ```js
  function Foo() {
    //..
  }

  var foo = new Foo();

  console.log( Foo.prototype === Object.getPrototypeOf( foo ) ); // true
  ```

## Constructor or Call?
- Functions themselves are **not** constructors
- Any function called with `new` keyword is said to be a constructor
- **Best practice:** Constructor functions use capital letters (*Not compulsory though*)
- Putting `new` keyword infront of a normal `function call` makes it a `constructor call`
- `constructor call` constructs an object in addition to whatever it was going to do
- constructor does **not** mean constructed by

Consider:

```js
function NothingSpecial() {
  console.log( "Don't mind me!" );
}

// Constructor call
var a = new NothingSpecial(); // Don't mind me!
a; // {}

// Function call
var b = NothingSpecial(); // Don't mind me!
b; // undefined
```

### Mechanics

Consider:

```js
function Foo() { /* .. */ }
// By default, function declaration gets a `Foo.prototype` object with `constructor` property
var a = new Foo();

a.constructor === Foo; // true

// Create a new prototype object without `constructor` property
Foo.prototype = { /* .. */ };

var b = new Foo();
b.constructor === Foo; // false
b.constructor === Object; // true

// Adding `.constructor` back to `Foo.prototype` object
Object.defineProperty( Foo.prototype, "constructor", {
  enumerable: false,
  writable: true,
  configurable: true,
  value: Foo
});

var c = new Foo();
c.constructor === Foo; //true
```

- `a.constructor` doesn't mean `a` has a property named `constructor` on itself
- `a.constructor === Foo` doesn't mean `a` was constructed by `Foo`
- It just happens that `a.constructor` points to `Foo`
- Behind the scene, delegation is working to find `.constructor` property
- By default, function declaration gets a `Foo.prototype` object with `constructor` property that references back to `Foo` function
- `a.constructor` ===> `Foo.prototype.constructor` (**Found**) -> Points to `Foo` function
- `b.constructor` ===> `Foo.prototype.constructor` ==> `Object.prototype.constructor` (**Found**) -> Points to `Object` function
- Best Practice: Avoid `.constructor` references where possible

## (Prototypal) Inheritance

- Traditionally we think inheritance to be relationship between two "classes", rather than between "class" and "instance"
- The figure below shows delegation from object (instance) `a1` to object `Foo.prototype`
- It also shows delegation from `Bar.prototype` to `Foo.prototype` that somewhat resembles Parent-Child class inheritance
- Arrows denotes delegation links rather than copy operations

![Prototypal Inheritance](Prototype.png "Prototypal Inheritance")

"Prototypal style" code:
```js
function Foo(name) {
  this.name = name;
}

Foo.prototype.myName = function() {
  return this.name;
};

function Bar(name, label) {
  Foo.call( this, name );
  this.label = label;
}

// Beware! Newly linked Bar.prototype will loose its default `Bar.prototype.constructor`
Bar.prototype = Object.create( Foo.prototype );

Bar.prototype.myLabel = function() {
  return this.label;
};

var b = new Bar( "b", "Object b" );
b.myName();  // b
b.myLabel(); // Object b
```

- `Object.create(..)` creates a "new" object and links that new object's (Eg: `Bar.prototype`) internal `[[Prototype]]` to the specified object (Eg: `Foo.prototype`)
- In short, make a new `Bar.prototype` object that links to `Foo.prototype` object as shown in figure

#### Misconception
```js
// 1. Default Bar.prototype
// Declaring a function creates a default `Bar.prototype` that links back to link to its default object
function Bar() { /* .. */ }

// 2. Linking Bar.prototype to Foo.prototype
// Removes the existing default Bar.prototype from #1
Bar.prototype = Object.create( Foo.prototype );

// 3. First Misconception
// Linking Bar.prototype to Foo.prototype
// Problem: Both of them points to the same object
//          Changing Bar.prototype will directly affect Foo.prototype
Bar.prototype = Foo.prototype;

// 4. Second Misconception
// Linking Bar.prototype to Foo.prototype
// Problem: Although it creates a new object linked to Foo.prototype,
//          new Foo() invokes a constructor call which might have
//          side effects (logging, setting values, adding properties to `this` etc)
Bar.prototype = new Foo();
```

#### ES6 solution
- Disadvantage of pre-ES6 approach: it throws away default existing `Bar.prototype`
- Advantage of ES6 approach: it modifies existing `Bar.prototype`

```js
// pre-ES6
Bar.prototype = Object.create( Foo.prototype );

// ES6+
Object.setPrototypeOf( Bar.prototype, Foo.prototype );
```

### Inspecting "Class" Relationships
- Inspecting an instance (object in JS) for its inheritance (delegation linkage in JS) is called introspection or reflection


1. `instanceof` operator:
  - Needs a function on the right hand side of operator
  - In the entire `[[Prototype]]` chain of `a`, does Foo.prototype ever appear?
  - Syntax: objectName `instanceof` FunctionName

2. `isPrototypeOf(..)` method: Cleaner approach
  - Finding relationship between two objects
  - Doesn't need functions at all
  - In the entire `[[Prototype]]` chain of `b`, does `a` ever appear?
  - Syntax: firstObjectName.isPrototypeOf( secondObjectName )

3. `Object.getPrototypeOf(..)` method
  - Directly retrieve the `[[Prototype]]` of an object
  - Syntax: Object.getPrototypeOf( a )

4. `__proto__` property (not standardized until ES6!)
  - A way to access the interal [[Prototype]] of an object
  - Pronounced as "dunder proto" (dunder unofficial term for double-underscore)
  - Syntax: objectName.__proto__

```js
function Foo() { /* .. */ }

var a = new Foo();

// 1. instanceof
a instanceof Foo; // true

var b = Object.create( a );

// 2. isPrototypeOf
a.isPrototypeOf(b); // true

// 3. Object.getPrototypeOf(..)
Object.getPrototypeOf( a ) === Foo.prototype; // true

// 4. __proto__
a.__proto__ === Foo.prototype; // true
```

## Object Links
- When a property/method doesn't exist in the object itself, the `[[Prototype]]` link tells the engine where to look next forming a 'prototype chain'

### `Create()` ing Links
- **Cut** the confusion of `.prototype` and `.constructor` to emulate classes to link objects
- **Best practice:** Use `Object.create(..)` to create links between proper objects
- Object with empty [[Prototype]] linkage will noth have any surprising effects from delegated properties/functions
  - Use case: Flat Data Storage (Dictionaries)

```js
var foo = {
  something: function() {
    console.log( "Cut the confusion" );
  }
};

// 1. Creates a link between `bar` and `foo`
var bar = Object.create( foo );

bar.something(); // Cut the confusion

// 2. Create object with an empty/null [[Prototype]] linkage
var dictionary = Object.create( null );
dictionary.cities = [ "New York", "Washington D.C" ];
console.log( dictionary.cities.length ); // 2
```

#### Object.create() Polyfilled
- Object.create(..) was added in ES5
- For systems prior to ES5, partial polyfill (doesn't support additional parameter to define properties) can be applied as below

```js
if(!Object.create) {
  Object.create = function(o) {
    // Throw away function F() to create object
    // and override its `.prototype` to point to the object we want to link to
    function F(){}
    F.prototype = o;
    return new F();
  };
}
```

### Links As Fallbacks?
- Using links between objects as fallback for missing properties is a **bad** practice
- ES6 introduced `Proxy` which can provide something of a "method not found" behavior
- Writing a fallback object just incase a child object can't handle some property/method is harder to understand and maintain

```js
var fallbackObject = {
  something: function() {
    console.log( "Magical moment!!" );
  }
};

var bar = Object.create( fallbackObject );

bar.something(); // Magical moment!!
```

**Better Approach:**  Without unexpected magical stuff, but still utilizing the benefit of `[[Prototype]]` delegation mechanism
```js

var someObject = {
  something: function() {
    console.log( "Cool!!" );
  }
};

var bar = Object.create( someObject );

bar.doSomething = function() {
  this.something();
};

bar.doSomething(); // Cool!!
```
