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

### Getters and Setters: Override default [[Get]] and [[Put]]
- Getters: Hidden function to retrieve a value
- Setters: Hidden function to set a value
- Getters and Setters are defined per-property level
- Property description that contains getters or setters or both: "Accessor Descriptor"
- For accessor descriptors, the `value` and `writable` characteristics of descriptors are ignored
- Best Practice: Always declare both getter and setter

```js
// 1. Object literal syntax get and set
var myObject = {
  // define getter for 'amount'
  // Just a convention of representing _amount_ internally
  get amount() {
    return this._amount_;
  },

  // define setter for 'amount'
  set amount(amount) {
    this._amount_ = "$" + amount;
  }
}

// 2. Explicit definition using defineProperty(..)
// Only getter is defined
Object.defineProperty(
  myObject, // target object
  "year", // new property
  { // Property Descriptor
    get: function() {
      return 2016;
    }
  }
);

myObject.amount = 200;
console.log( myObject.amount ); // $200
myObject.amount = 500;
console.log( myObject.amount ); // $500


console.log( myObject.year ); // 2016
myObject.year = 1900;
console.log( myObject.year ); // 2016  [No setter defined. Cannot overwrite.]
```


### Existence of a Property in an Object
- `in` operator will check if the property is in the object, or higher level of the [[Prototype]] chain
- hasOwnProperty(..) will only check if property is in the direct object
- hasOwnProperty(..) is accessible to all normal objects via delegation to Object.Prototype
- But if object was created using Object.create(null), it won't have access to hasOwnProperty(..)
  In such case, we need to use *explicit binding*. Eg: Object.prototype.hasOwnProperty.call( myObject, "prop" )

```js
var myObject = {
  a: 2
};

("a" in myObject); // true
("b" in myObject); // false

myObject.hasOwnProperty( "a" ); // true
myObject.hasOwnProperty( "b" ); // false
```


### Enumeration
- `for..in` loops in arrays can give unexpected results if it contains enumerable properties which are non-numeric
- Best Practice:
  - Use `for..in` loops only on objects
  - Use traditional `for(i=0; i<10; i++)` loop for arrays
- Use `propertyIsEnumerable(..)` to test if a property exists directly on the object and set to `enumerable:true`
- Use `Object.keys(..)` to get all enumerable properties of direct object
- Use `Object.getOwnPropertyNames(..)` to get all properties whether enumerable or not

```js
var obj = {};

// 1. Add 'a' as a enumerable property of obj
Object.defineProperty( obj, "a", {
  enumerable: true,
  value: 2
});

// 2. Add 'b' as a NON-enumerable property of obj
Object.defineProperty( obj, "b", {
  enumerable: false,
  value: 3
});

console.log( obj.a ); // 2
console.log( obj.b ); // 3

// 3. `in` operator property check, traverses Prototype chain
( "b" in obj ); // true

// 4. hasOwnProperty(..) property check in direct object
obj.hasOwnProperty( "b" ); // true

// 5. for..in loop. Doesn't include NON-enumerable property "b"
for (var key in obj) {
  console.log( "Key: " + key + ", Value: " + obj[key] );
}
// Output: Key: a, Value: 2

// 6. propertyIsEnumerable(..)
obj.propertyIsEnumerable( "a" ); // true
obj.propertyIsEnumerable( "b" ); // false

// 7. Object.keys(..)
Object.keys( obj ); // ["a"]

// 8. Object.getOwnPropertyNames(..)
Object.getOwnPropertyNames( obj ); // ["a", "b"]
```

### Iteration

#### Standard `for` loop: Iterating over indices of an array
```js
var myArray = [1, 2, 3];

for (var i = 0; i < myArray.length; i++) {
  console.log( myArray[i] );
}
// 1 2 3
```


#### forEach(..): Iteration helper for arrays

- Iterate over all values by ignoring any callback return values

```js
var myArray = [5, 10, 15];

// index and array are optional parameters
function logArrayElements(element, index, array) {
  console.log( "Index " + index + ": " + element );
}

myArray.forEach( logArrayElements );
// "Index 0: 5"
// "Index 1: 10"
// "Index 2: 15"
```

#### every(..): Iteration helper for arrays

- Iterate over the values until the end or the callback returns a `false` value

```js
var fives = [5, 10, 15];
var primes = [2, 3, 5];

// index and array are optional parameters
function isMultipleOfFive(element, index, array) {
  return element%5 === 0;
}

console.log(fives.every( isMultipleOfFive )); // true
console.log(primes.every( isMultipleOfFive )); // false
```

#### Arrow functions: Shorter syntax
```js
var fives = [5, 10, 15];

console.log(fives.every( element => element%5 === 0 )); // true
```

#### some(..): Iteration helper for arrays

- Iterate over the values until the end or the callback returns a `true` value

```js
var fives = [5, 10, 15];
var odds = [1, 3, 5];

// index and array are optional parameters
function isEven(element, index, array) {
  return element%2 === 0;
}

console.log(fives.some( isEven )); // true
console.log(odds.some( isEven )); // false
```

### ES6 adds `for..of` loop
- Loops through value instead of indices
- Uses `@@iterator` [Symbol.iterator] to call `next()` method once for each loop iteration
- Arrays have default support of `for..of` loops
- Objects can also use `for..of` loop by defining [Symbol.iterator] property

```js
var fives = [5, 10, 15];
var it = fives[Symbol.iterator]();

it.next(); // { value:5, done:false }
it.next(); // { value:5, done:false }
it.next(); // { value:5, done:false }
it.next(); // { value:undefined, done:true }
```

Consider: Adding custom `@@iterator` to 'randoms' object

```js
// Adding custom @@iterator property using literal syntax
var randoms = {
  [Symbol.iterator]: function() {
    return {
      next: function() {
        return { value: Math.floor((Math.random()*10)) };
      }
    };
  }
};

var randoms_pool = [];
for (var n of randoms) {
  randoms_pool.push( n );

  //Breaking point
  if (randoms_pool.length === 5) break;
}

console.log( randoms_pool ); // [1, 4, 6, 1, 8]
```
