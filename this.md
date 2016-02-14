## this
- `this` is a binding made when a function is invoked
- What `this` refers to is entirely based by the call-site where the function is called

### Consider: Explicitly passing object reference (context) around
```js
function greet(context) {
  console.log("Hi " + context.name);
}

var foo = {
  name: "Foo"
};

var bar = {
  name: "Bar"
};

greet( foo ); // Hi Foo
greet( bar ); // Hi Bar

```

### Elegant way of passing object reference implicitly using `this`
```js
function greet() {
  console.log("Hi " + this.name);
}

var foo = {
  name: "Foo"
};

var bar = {
  name: "Bar"
};

greet.call( foo ); // Hi Foo
greet.call( bar ); // Hi Bar
```
