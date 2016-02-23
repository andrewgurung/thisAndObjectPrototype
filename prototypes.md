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
