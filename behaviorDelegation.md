## Behavior Delegation
- A fundamental and critical concept to understand in Javascript is about **"objects being linked to other objects"**
- Shifting from `class/inheritance` design pattern to `behavior delegation` design pattern

### Class Theory
- Define a parent (base) class like `Task` defining shared behavior
- Then define child classes `XYZ` and `ABC`, both of which inherit from `Task` and each adds specialized behavior
- Encourages method overriding aka polymorphism perhaps even calling `super`
- You can instantiate one or more `copies` of child class `XYZ` to perform task `XYZ`
  - These instances will have `copies` of both `Task` and `XYZ`
  - After instantiation, you generally interact with these instances (and **not** the classes)

```js
class Task {
  id;

  // constructor Task()
  Task(ID) { id = ID; }
  outputTask() { output( id ); }
}

class XYZ inherits Task {
  label;

  // constructor XYZ()
  XYZ(ID, Label) {
    super( ID );
    label = Label;
  }

  outputTask() {
    super();
    output( label );
  }
}
```

### Delegation Theory: OLOO (Objects linked to other objects)
- Define an object (not a class nor a function) called `Task` with concrete behavior that includes utility methods
- Then for each task, define `XYZ` and `ABC` that hold task-specific data/behavior
- Rather than `composing` them together via `class copies`, we keep them in their separate objects
- Link `XYZ` and `ABC` objects to the `Task` utility object allowing them to delegate when needed
- Object.create(..) is used to [[Prototype]]- delegate to Task

```js
var Task = {
  setID: function(ID) { this.id = ID; },
  outputID: function() { console.log( this.id ); }
};

// make `XYZ` delegate to `Task`
var XYZ = Object.create( Task );

// `XYZ` task specific behavior
XYZ.prepareTask = function(ID, Label) {
  this.setID ( ID );
  this.label = Label;
};

XYZ.outputTaskDetails = function() {
 this.outputID();
 console.log( this.label );
};

// ABC = Object.create( Task );
```

#### Notes on OLOO style code:

1. State should be on the delegator (`XYZ`), not on the delegate (`Task`)
   Eg: Both `id` and `label` data members are direct properties on `XYZ`
2. Avoid naming things the same where possible, because name collision (overriding/polymorphism) creates brittle syntax
   Eg: `Task.outputID()`, `XYZ.outputTaskDetails()`
3. `this.setID(ID);` inside of `XYZ` object first looks on `XYZ` for `setID(..)`  
   But since it doesn't find it on the object itself, by `[[Prototype]]` delegation, it can follow the link to `Task`  
   Moreover, because of implicit call-site `this` binding, even though `setID(..)` was found in `Task` object, `this` will refer to our `XYZ` call-site `this` object
4. Mutual Delegation is not allowed by the Javascript engine. If you make `B` linked to `A`, and then try to link `A` to `B`, you will get an error


## OO (Prototypal Inheritance) VS OLOO Style
- Both style takes same advantage of `[[Prototype]]` delegation


### 1. OO (classical Prototypal Inheritance) Style
  - Delegation from `b1` to `Bar.prototype` to `Foo.prototype`
  - Tries to look like classes, with constructors and prototypes and `new` calls

```js
function Foo(who) {
  this.me = who;
}

Foo.prototype.identify = function() {
  return "I am " + this.me;
};

function Bar(who) {
  Foo.call( this, who );
}

Bar.prototype = Object.create( Foo.prototype );

Bar.prototype.speak = function() {
  console.log( "Hi, " + this.identify() );
};

var b1 = new Bar( "b1" );
b1.speak(); // Hi, I am b1

var b2 = new Bar( "b2" );
b2.speak(); // Hi, I am b2
```

![Prototypal Inheritance](PrototypalInheritance.png "Prototypal Inheritance")


### 2. OLOO Style
  - Delegation from `b1` to `Bar` to `Foo`
  - Only cares about **objects linked to other objects**
  - OLOO is just cutting out the middle-man (constructor/new mechanisms)
  - OLOO supports the principle of separation of concerns, where creation and initialization are done in separate calls  
    Eg: Creation: `Object.create(Bar)` and Initialization: `b1.init('b1')`

```js
var Foo = {
  init: function(who) {
    this.me = who;
  },
  identify: function() {
    return "I am " + this.me;
  }
};

var Bar = Object.create( Foo );

Bar.speak = function() {
  console.log( "Hi, " + this.identify() );
};

var b1 = Object.create( Bar );
b1.init( "b1" );
b1.speak(); // Hi, I am b1

var b2 = Object.create( Bar );
b2.init( "b2" );
b2.speak(); // Hi, I am b2
```

![OLOO Style](oloo.png "OLOO Style")
