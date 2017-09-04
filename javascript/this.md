# Understanding `this` as Dependent of the Lexical Context of Invocation

This post aims make the `this` variable feel more predictable and consistent.

## What Is `this`?

The `this` variable is a variable given to us by the Javascript engine to be used during an execution context. `this` does not need to be defined or assigned a value before being called. Take this example:

```javascript
const myFunction = function () {
  console.log(this); // `this` can be called being defined or assigned a value
};

myFunction();
```

The function `myFunction` can be invoked without errors, because `this` is automatically assigned a value, depending on the context.

## Two Ways to Invoke a Function

There are 2 ways to invoke a function.
1. Function Style
```javascript
myFunction();
```
2. Method Style
```javascript
mySomething.myFunction();
```
The `this` variable will return a different value, depending on how you invoke a function.

## When a Function Is Invoked Function style

Whenever a function is invoked function style, the `this` variable will always return the global object.
Take the same example we had earlier:

```javascript
const myFunction = function () {
  console.log(this); // `this` returns the global object
};

myFunction();
```

For the example above, `this` returns the global object. Copy and paste the code above into node or Chrome dev tools and you'll see what I mean. Whenever a method is invoked function style, the global object will be the return value for `this`.

Another example:

```javascript
const person1 = {
  name: 'John Smith',
  age: 30,
  rebirth() {
    this.age = 0;
  }
}
```
In this example, we might intuitively think that calling the `rebirth` method will reset the age of `person1` to `0`. However, if we go through a few loop holes to invoke `rebirth` function style, you will see that the `this` variable returns the global object.
```javascript
const person1 = {
  name: 'John Smith',
  age: 30,
  rebirth() {
    this.age = 0;
  }
}

const rebirth = person1.rebirth;
rebirth(); // invoking rebirth function style
console.log(person1); // {name: "John Smith", age: 30, rebirth: ƒ}
```

Try running this code in your favorite Javascript engine and you will see that `person1.age` is untouched. However, look up the age attribute of the global object (`global.age` in Node and `window.age` in any browser) and you will see that your global object suddenly has an `age` of `0`.
