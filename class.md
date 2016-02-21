### Class
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
