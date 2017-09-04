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

## When a Function Is Invoked Function Style

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

Try running this code in your favorite Javascript engine and you will see that `person1.age` is untouched. However, look up the age attribute of the global object (`global.age` in Node and `window.age` in any browser) and you will see that your global object has an `age` of `0`. This is because any function, when invoked function style, has the global object assigned to `this`.

Now some of you might argue that this line `const rebirth = person1.rebirth;` somehow messes with the `this` variable. Keep in mind that in Javascript, whenever a variable is assigned a function (in this case, `const rebirth` is assigned the function `rebirth`), the variable is only passed a reference to that function. `const rebirth` is assigned only a reference, and it points to the exact same function in memory as `person1.rebirth`. `const rebirth` is exactly the same as `person1.rebirth`.

## When a Function Is Invoked Method Style

The following code will set `person1.age` to 0.

```javascript
const person1 = {
  name: 'John Smith',
  age: 30,
  rebirth() {
    this.age = 0;
  }
}

person1.rebirth(); // `this` variable is assigned the `person` object
```

Whenever a function is invoked method style, the `this` variable is assigned the receiver of the method, which, in this case, is `person1`. Take this example:

```javascript
const person1 = {
  name: 'John Smith',
  age: 30,
  rebirth() {
    this.age = 0;
  }
}

const justAnArray = [1, 2, 3];
justAnArray.rebirth = rebirth;
justAnArray.rebirth();
```

The code above will set `justAnArray.age` to be `0`. Arrays normally don't have an age attribute, but `justAnArray` now has an age attribute, and it has a value of `0`. Again, you might be arguing that `justAnArray.rebirth = rebirth;` somehow messes with the function itself. Just trust me that they are the exact same function in memory. I'll prove it another time.

## `bind`, `apply`, and `call`

`bind`, `apply`, and `call` simply reassigns a new value to the `this` variable.

## Take-away

Whenever a function is invoked, the Javascript engine assigns an object to the `this` variable. Which object does the engine assign to the `this` variable depends completely on how the variable is invoked, and has absolutely nothing to do with how a variable is defined.

[Home][home]

[home]: ../README.md
