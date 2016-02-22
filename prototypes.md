## [[Prototype]]
- Objects in Javascript have an internal property `[[Prototype]]`, which is simply a reference to another object
- In most cases `[[Prototype]]` is a non-null value

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
