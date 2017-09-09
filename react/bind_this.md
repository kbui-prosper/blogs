# The reason why you have to `bind(this)` for callback functions in React - by Khai Bui

## The conventional way of handling DOM events

Let's consider a very simple React component that displays the length of the value inside an `<input>` field

```javascript
class StringLength extends React.Component {
  constructor () {
    super();
    this.state = {
      inputLength: 0
    }
  }

  inputChange (event) {
    this.setState({
      inputLength: event.target.value.length
    });
  }

  render () {
    return (
      <div>
      <input type='text'
             onChange={this.inputChange.bind(this)}/>
        {this.state.inputLength}
       </div>
    )
  }
}
```

This React component initializes with its `state` being `{ inputLength: 0 }`, and renders an `<input>` field of type `text`. Every time a change is made to this input field, `onChange` attribute triggers `this.inputChange`, which simply sets a new state. The `render` method also renders the `this.state.inputLength`. You can see this code in action by clicking [here][demo].

[demo]: https://codepen.io/khaivubui/full/JygGKZ/

Now, note that inside the `<input>` tag, for the `onChange` handler, the `inputChange` function is bound to `this`. If we don't add `bind(this)` for the function, `this.setState` would be called on `undefined`, instead of the `StringLength` component that we created. But why is this the case? Shouldn't the `this` variable be dependent on how a function is invoked? It looks like the function `inputChange` is invoked method style, and the receiver of the method is `this`, which should be the `StringLength` component. So why does `this` go haywire unless we bind it?

## What's really going on under the hood of the render function

JSX is actually not real javascript, and is compiled to real javascript using Babel. Babel has an amazing online [REPL][babel-repl] that we can play around with. Let's try it out and see what's really going on under the hood. [Click here][babel-repl].

[babel-repl]:https://babeljs.io/repl/#?babili=false&browsers=&build=&builtIns=false&code_lz=MYGwhgzhAEDKAuAnAlgOwOYBkCmH4AtpsAPeXAExgCVsxh4A6AYQHsBbABxdV3mgG8AUNGjBuEJAFd6LRNAAUASgHCR0CJI7ZESgNyqRBZBAYSwZaAF4VatWg6T4OPPgBc0AAwHoAX1V_Ve0cmfDAMbAVsADdeZSFbIxMIbHgEc2x5eNtoIKdcdAJ3aN4GeDBEdBSGKLAQSWwGEHyCbx9FfREAkUQKbQU47x74SURUBW8RAB5yZCiAPgnoSdzoeABPLUsAcjJSLcXskW4QsMrLfkSGXJPwhgAjNHJ5RMUfAHoFw5EL_GNTMrIV1QDjyLj8X0mbxm828in8gh8QA&debug=false&circleciRepo=&evaluate=false&lineWrap=false&presets=react&prettier=false&targets=&version=6.26.0

Spending a few minutes analyzing the Babel compilation, we can see that the `constructor` function and `inputChange` function are exactly the same. The `render` fuction, however, is quite different. Looking at the right side of the Babel REPL, you can see that our `render` function is compiled to the following:
```javascript
render() {
  return React.createElement(
    'div',
    null,
    React.createElement(
      'input',
      { type: 'text', onChange: this.inputChange.bind(this) }
    ),
    this.state.inputLength
  );
}
```

Most of the things going on here is beyond the scope of this analysis. The key takeaway here is that `this.inputChange` is passed into the React element as part of a POJO (Plain Old Javascript Object). Specifically, this object:
```javascript
{
  type: 'text',
  onChange: this.inputChange.bind(this)
}
```
This approach assigns the `inputChange` method to the `onChange` key of this object, and passes this object into `React.createElement`. Because of this, when this method is invoked within the React element, the `this` binding is lost.

## The work-around: Arrow Functions

We developers naturally shy away from repeatedly typing the same code again and again. In the case of React, whenever an event handler, for example, `inputChange`, is passed into a React element/component, we have to do `.bind(this)`. This leads to multiple occurrences of the code `.bind(this)` in React apps, which doesn't appear very DRY. Intuitively, arrow functions come to mind when finding a solution to DRY up our code. Before reading on about the use of the arrow functions as a solution to avoid binding `this`, make sure you take a look at [this analysis][js-arrow-this] of arrow functions.

[js-arrow-this]: ../javascript/arrow_and_this.md

Consider approach 1:

```javascript
StringLength.prototype.inputChange = (e) => {
  this.setState({
    inputLength: event.target.value.length
  });
}
```

This approach replaces our regular prototype function `inputChange` with an arrow function version. This approach does not work, because, for arrow functions, `this` binding happens when the function is __created__, and in the context in which `inputChange` is created, the `this` variable is the global object (either `window` or `global`), and thus `inputChange` will permanently have the global object assigned to `this`. With that being the case, `this.setState` would never work correctly.

Consider approach 2, where we set inputChange to __return__ an arrow function:
```javascript
class StringLength extends React.Component {
  // ...
  inputChange () {
    return (e) => {
      this.setState({
        inputLength: event.target.value.length
      });
    }
  }

  render () {
    return (
      <div>
      <input type='text'
             onChange={this.inputChange()}/>
        {this.state.inputLength}
       </div>
    )
  }
}
```

In this approach, the `inputChange` function returns an arrow function. For the context in which the arrow function is created, `this` refers to the `StringLength` component, which means we will get the correct `this` binding. Also note that for `onChange={this.inputChange()}`, we are invoking the `inputChange` function, not just passing it in. Invoking the function will return the arrow function `(e) => { //... }`, which is what we want to pass in as the event handler for `onChange`.

Consider approach 3:

```javascript
class StringLength extends React.Component {
  constructor () {
    super();
    this.state = {
      inputLength: 0
    }
    this.inputChange = (e) => {
      this.setState({
        inputLength: event.target.value.length
      });
    }
  }

  render () {
    return (
      <div>
      <input type='text'
             onChange={this.inputChange.bind(this)}/>
        {this.state.inputLength}
       </div>
    )
  }
}
```

In this example, `inputChange` is created inside `constructor`, guaranteeing that the `this` binding is pointing to the correct instance of `StringLength`. This approach also creates a new arrow function for every instance of `StringLength`, same as approach 2.

So now, some of you might be asking yourself: Wouldn't it be very space inefficient to create a new function for every instance of `StringLength`?

## The truth about the good old correct way

Let's revisit our original approach:

```javascript
class StringLength extends React.Component {
  constructor () {
    super();
    this.state = {
      inputLength: 0
    }
  }

  inputChange (event) {
    this.setState({
      inputLength: event.target.value.length
    });
  }

  render () {
    return (
      <div>
      <input type='text'
             onChange={this.inputChange.bind(this)}/>
        {this.state.inputLength}
       </div>
    )
  }
}
```

This approach, at first, might appear to be using the same `inputChange` callback for every instance of `StringLength`. So if you have 1000 `StringLength` instances on your page for some reason, you might think they all use the same `inputChange`, and being space efficient. The cold harsh truth is, every time `bind(this)` is called, a new function is created. According to official [MDN Documentation][mdn-bind], `bind()` returns a new function. `bind()` does not hold a reference to the old function, and simply is a brand new function in memory. So with this in mind, we might as well use arrow functions. ¯\\\_(ツ)_/¯

[Home][home]

[home]: ../README.md
[mdn-bind]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind
