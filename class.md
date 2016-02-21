
## Class
- Classes are a design pattern
- Classes in JS behaves very differently than other languages
- Classes means copies
- Traditional class:
  - When a class is instantiated, the behavior of class is copied to its instance (object)
  - When a child inherits from a parent, the behavior of a parent class is copied to its child
- Polymorphism means having different functions in multiple levels of an inheritance chain with same function name
  - Polymorphism doesn't rely on referential link from child to parent
  - Instead Polymorphism is a result of `copy behavior` (behavior of a parent class is copied to its child)
- Javascript does not automatically create copies as traditional class implies
- Diamond Problem: Multiple Inheritance  
  Consider:
  - Class D inherits from two parents B and C (multiple inheritance)
  - Both B and C inherits from parent class A
  - Class A provides a `drive()` method, and both B and C override(polymorph) that method?
  - When D references `drive()`, which version should it use? `B:drive()` or `C:drive()`

```
*   A
   / \
  B   C
   \ /
    D
```

## Mixins
- A technique of copying properties and functions from a source object to its target object
- Inheritance or instantiation does not copy objects in Javascript
- Javascript does not have `classes` to instantiate only objects
- Instead objects are linked to each other
- JS Developers fakes copy behavior of traditional classes (as in other languages) with `Mixins`
- Two types of `Mixins`:
  - Explicit Mixins
  - Implicit Mixins

### Explicit Mixins
- Explicit Mixins are not same as class-copy behavior
- Copying objects and functions are not real copies, instead duplication of shared references
- Copies a property from one object to another
- Explicitly mixing two or more sources into target object results in multiple inheritance

Consider copying behavior from 'Vehicle' to 'Car'. Since Javascript doesn't do it automatically, we create a utility that manually copies.
Many libraries called this utility `extend`. For eg: JQuery has `$.extend(..)`
But we will call it `mixin(..)`

```js
// Vastly simplified mixin(..)
function mixin( sourceObj, targetObj ) {
  for (var key in sourceObj) {
    // Only copy properties that are missing
    if (!(key in targetObj)) {
      targetObj[key] = sourceObj[key];
    }
  }

  return targetObj;
}

var Vehicle = {
  engines: 1,

  ignition: function() {
    console.log( "Turning on my engine." );
  },

  drive: function() {
    this.ignition();
    console.log( "Steering and moving forward!" );
  }
};

var Car = mixin( Vehicle, {
  wheels: 4,

  drive: function() {
    Vehicle.drive.call( this ); // Explicit pseudopolymorphism
    console.log( "Rolling on all " + this.wheels + " wheels!");
  }
} );

console.log( Car.drive() );
// Output:
//---------------------------------
// "Turning on my engine."
// "Steering and moving forward!"
// "Rolling on all 4 wheels!"
```

- No classes are involved
- We are only adding properties and functions from Vehicle to Car object
- Functions are not copied but references to functions are duplicated
- Since Car already had `drive(..)` property(function), it wasn't overriden by Vehicle's `drive(..)` property
- Explicit pseudopolymorphism: `Vehicle.drive.call( this )` since both Vehicle and Car has `drive()` function, we have to use absolute reference
- Explicit pseudopolymorphism causes brittle manual/explicit linkage in every function where you need such a reference
- Best Practice: Avoid Explicit Mixin where possible

#### Parasitic Inheritance


### Implicit Mixins
- Closely related to explicit pseudopolymorphism
