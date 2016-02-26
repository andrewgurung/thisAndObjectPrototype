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
