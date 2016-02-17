## Objects

### Syntax
1. Literal Form (Preferred way for creating object)

```js
var foo = {
  age: 20
  // ..
};
```

2. Constructor Form

```js
var foo = new Object();
foo.age = 20;
```

### Six Primary Language Types: Not objects themselves
* Simple primitives (string, boolean, number, null and undefined) are not themselves objects
* Bug: `typeof` null returns "object" incorrectly. In fact, `null` is its own primitive typeof
* `function` is a subtype of `object` (a.k.a callable object)


1. string
2. number
3. boolean
4. null: Doesn't have object wrapper for coercion
5. undefined: Doesn't have object wrapper for coercion
6. object

Consider:
```js
var strPrimitive = "Foo";
typeof strPrimitive; // "string"

// Automatically coerces primitive "string" to String object wrapper
strPrimitive.length; // 3
```
### Built-in Objects
- String
- Number
- Boolean
- Object: Never primitives no matter how it's created (literal or constructor form)
- Function
- Array: Never primitives
- Date: Must use constructor form to create dates
- RegExp: Never primitives
- Error: Rarely created explicitly. Can be created with new Error(..)


1. `object` subtypes are known as built-in objects
2. Though it looks like class, these are just built-in functions used as constructor (Invoked with `new` keyword that returns a newly constructed object)

Consider:
```js
var strPrimitive = new String( "foo" );
typeof strPrimitive; // "object"
```

### Object accessors
Ways of accessing object contents
1. dot `.` operator: Property access
2. bracket `[ ]` operator: Key access
3. [".."] can take any UTF-8/Unicode compatible string. Eg ["Super-Fun!"]
4. . operator requires valid Identifier property
5. Using [".."], we can pass key programmatically. Eg myObject[prefix + name]
6. Property names are always string. Any other value will be converted to a string


```js
var foo = {
  age: 20
};

console.log( foo.age );    // 20
console.log( foo["age"] ); // 20
```

### ES6 computed property names

```js
var prefix = "foo";

var obj = {
  [prefix + "bar"]: "hello",
  [prefix + "baz"]: "world",
}

console.log( obj["foobar"] ); // hello
console.log( obj["foobaz"] ); // world
```

### Arrays
- Numeric indexing to store values
- Arrays are objects, so you can add properties. But not recommended.
- Adding string property doesn't change the length of array. But adding numeric looking property does!

Consider:
```js
var fooArray = [ "foo", 42, "bar" ];
fooArray.length; // 3

// 1. Adding string property
fooArray.baz = "baz"
fooArray.length; // 3

// 2. Adding numeric looking property
fooArray["3"] = "baz";
fooArray.length; // 4
```

### Duplicating Objects
- ES6 has defined `Object.assign(source, target[s])` to perform a shallow copy

Consider:
```js
function greet() {
 console.log( "Hi" );
}

var myObject = {
 a: 20,
 b: greet
};

var duplicateObject = Object.assign( {}, myObject );
console.log( myObject.a ); // 2
console.log( myObject.b === duplicateObject.b ); // true due to shallow copy refers to same location
```

### Property Descriptors
- ES5 allowed properties to have characteristics

```js
var myObject = {
 a: 20,
};

Object.getOwnPropertyDescriptor( myObject, "a" );
/*
 {
  value: 20,
  writable: true,
  enumerable: true,
  configurable: true
}
*/
```

- Add Property: Generally rarely used
  - Writable: If set to false, trying to overwrite property value will fail silently. Or throw a `TypeError` in strict mode
  - Enumerable: If set to false, the property won't show up in a `for .. in` loop
  - Configurable: Be careful, one way traffic. Cannot delete property afterward. But still allows to modify properties

Consider:
```js
Object.defineProperty( myObject, "name", {
  value: "Foo",
  writable: true,
  enumerable: true,
  configurable: true
} );
console.log( myObject.name ); // Foo
```

### Immutability: Ways of achieving immutability
1. Object constant: writable: false, configurable: false
  - Turns a property to a constant

  ```js
  var myObject = {};

  Object.defineProperty( myObject, "CONSTANT_LUCKY_NUM", {
    value: 7,
    writable: false,
    configurable: false
  });
  ```

2. Prevent extensions: Object.preventExtensions(..)
  - Prevents from adding new properties

  ```js
  var myObject = {
    a: 2
  };
  Object.preventExtensions( myObject );
  myObject.b = 3;
  console.log( myObject.b ); // undefined
                             // TypeError (in Strict Mode)
  ```

3. Seal: Object.seal(..)
  - calls Object.preventExtensions(..) and also
  - sets configurable: false
  - Prevents from adding new properties
  - Prevents reconfiguring and deleting properties
  - But still allows to modify properties

4. Freeze: Object.freeze(..)
  - Highest level of immutability
  - calls Object.seal(..) and also
  - sets writable: false

### [[Get]]
- During a property access, the code internally performs a [[Get]] operation which inspects the object first for a property, then traverses to the [[Prototype]] chain.
- If nothing is found, returns the value `undefined`
- Hard to determine if a value of a property is `undefined` explicitly or it simply returned `undefined` (not found).
  Eg. Even though `myObject.b` and `myObject.z` returns `undefined`, they are NOT same

```js
var myObject = {
  a: 2,
  b: undefined
};

console.log( myObject.a ); // 2
console.log( myObject.b ); // undefined
console.log( myObject.z ); // undefined
console.log( x );          // ReferenceError
```

### [[Put]]
- Behaves differently based on different factors such as access or data descriptor, writable or not, strict or non-strict mode etc.

### Getters and Setters
