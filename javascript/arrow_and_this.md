Before reading this article, make sure you have a strong understanding of [the `this` variable][js-this] first.

This article also assumes the reader to have some understanding of arrow function and its syntax.

[js-this]: this.md

# Understanding `this` in the Context of Arrow Functions - by Khai Bui

## Arrow Functions vs Classic JS Functions When Invoked Function Style

Consider the following code:

```javascript
const apple = {
  name: 'Apple',
  generateArrowThis () {
    // `this` in here returns the `apple` object
    return () => console.log(this);
  },
  generateClassicThis () {
    // `this` in here returns the `apple` object
    return function () {
      console.log(this);
    };
  }
};

const arrowThis = apple.generateArrowThis();
const classicThis = apple.generateClassicThis();

arrowThis(); // returns {name: "Apple", generateArrowThis: ƒ, generateClassicThis: ƒ}
classicThis(); // returns the global object
```

A few things happened in the code snippet above. First, the `apple` object is created, and it has 3 attributes: a `name` that we can use to identify this object, a `generateArrowThis` function that returns an arrow function, a `generateClassicThis` that returns a classic JS function.

Then, 2 global variables are created, `arrowThis` and `classicThis`, in order to store the arrow functions and the classic functions.

When we invoke `arrowThis`, the `apple` object is returned. When we invoke `classicThis`, the global object is returned. Go ahead and try it in your favorite JS environment. Keep this result in mind as we move forward.

## Arrow Functions vs Classic JS Functions When Invoked Method Style

Consider the following code:

```javascript
const apple = {
  name: 'Apple',
  generateArrowThis () {
    // `this` in here returns the `apple` object
    return () => console.log(this);
  },
  generateClassicThis () {
    // `this` in here returns the `apple` object
    return function () {
      console.log(this);
    };
  }
};

apple.arrowThis = apple.generateArrowThis();
apple.classicThis = apple.generateClassicThis();

apple.arrowThis(); // returns {name: "Apple", generateArrowThis: ƒ, generateClassicThis: ƒ}
apple.classicThis(); // returns {name: "Apple", generateArrowThis: ƒ, generateClassicThis: ƒ}
```

This code snippet is almost exactly the same as the first code snippet, with the distinction that `arrowThis` and `classicThis` were invoked method style. Both functions returned the `apple` object. Go ahead and try it out in your favorite JS environment. Keep the result in mind as we move forward.

## Arrow Functions vs Classic JS Functions When Invoked Method Style on Unrelated Objects

Consider the following code:

```javascript
const apple = {
  name: 'Apple',
  generateArrowThis () {
    // `this` in here returns the `apple` object
    return () => console.log(this);
  },
  generateClassicThis () {
    // `this` in here returns the `apple` object
    return function () {
      console.log(this);
    };
  }
};

const justAnArray = [1, 2, 3]

justAnArray.arrowThis = apple.generateArrowThis();
justAnArray.classicThis = apple.generateClassicThis();

justAnArray.arrowThis(); // returns {name: "Apple", generateArrowThis: ƒ, generateClassicThis: ƒ}
justAnArray.classicThis(); // returns [1, 2, 3, arrowThis: ƒ, classicThis: ƒ]
```

In this code snippet, I created a simple array, and assign it to the variable `justAnArray`. Then I assign the same 2 functions as attributes to `justAnArray`, in order to allow myself to invoke these functions method style. Notice that this time, invoking `arrowThis` still returns the `apple` object, while invoking `classicThis` returns `justAnArray`.

## The Take-away

Across all circumstances, `arrowThis` returns the `apple` object, while `classicThis` returns a different object every time, depending on how it is called. This leads to, perhaps, one of the most important behavior differences between arrow functions and classic functions:
* __For arrow functions, `this` variable is determined by the execution context of the definition of the function.__
* __For classic functions, `this` variable is determined by the method of the invocation of the function.__

Let me explain...

## For arrow functions, `this` variable is determined by the execution context of the definition of the function.

The execution context of definition is the context in which a function is defined. In our example, `() => console.log(this)` is defined inside a function inside the `apple` object. In this context, `this` is equal to the `apple` object. Therefore, invoking `arrowThis` will always return `apple`, regardless of how it is invoked. This is because the arrow function was defined in our code where `this` variable points to the `apple` object

## For classic functions, `this` variable is determined by the lexical context of the invocation of the function

The method of invocation is how a function is invoked in our code. For example, `classicThis`, when invoked Function Style, returns the global object. `classicThis`, when invoked Method Style, returns the receiver of the method. If this part is confusing, please re-read [the `this` variable][js-this].

[Home][home]

[home]: ../README.md
