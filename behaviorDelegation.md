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
