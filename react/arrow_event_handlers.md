# Using Arrow Functions to Avoid Binding `this` for Callback Functions in React

## The conventional way of handling DOM events

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

[Live Version][demo]

[demo]: https://codepen.io/khaivubui/full/JygGKZ/

## The work-around: Arrow Functions

We developers naturally shy away from repeatedly typing the same code again and again. In the case of React, whenever an event handler, for example, `inputChange`, is passed into a React element/component, we have to do `.bind(this)`. This leads to multiple occurrences of the code `.bind(this)` in React apps, which doesn't appear very DRY. Intuitively, arrow functions come to mind when finding a solution to DRY up our code. Before reading on about the use of the arrow functions as a solution to avoid binding `this`, make sure you take a look at [this analysis][js-arrow-this] of arrow functions.

[js-arrow-this]: ../javascript/arrow_and_this.md

## Approach 1:

```javascript
StringLength.prototype.inputChange = (e) => {
  this.setState({
    inputLength: event.target.value.length
  });
}
```

This approach replaces our regular prototype function `inputChange` with an arrow function version. This approach does not work, because, for arrow functions, `this` binding happens when the function is __created__, and in the context in which `inputChange` is created, the `this` variable is the global object (either `window` or `global`), and thus `inputChange` will permanently have the global object assigned to `this`. With that being the case, `this.setState` would never work correctly.

## Approach 2

Consider approach 2, where we set `inputChange` to __return__ an arrow function:
```javascript
class StringLength extends React.Component {
  // ...
  inputChange () {
    // `this` in here is the `StringLength` component
    return (e) => { // return an arrow function
      this.setState({ //`this` in here is still the `StringLength` component
        inputLength: event.target.value.length
      });
    }
  }

  render () {
    return (
      <div>
      <input type='text'
             onChange={this.inputChange()}/> // invoking `inputChange`, which returns an arrow function whose `this` is permanently bound
        {this.state.inputLength}
       </div>
    )
  }
}
```

In this approach, the `inputChange` function returns an arrow function. For the context in which the arrow function is created, `this` refers to the `StringLength` component, which means we will get the correct `this` binding. Also note that for `onChange={this.inputChange()}`, we are invoking the `inputChange` function, not just passing it in. Invoking the function will return the arrow function `(e) => { //... }`, which is what we want to pass in as the event handler for `onChange`. This approach works.

## Approach 3

```javascript
class StringLength extends React.Component {
  constructor () {
    super();
    this.state = {
      inputLength: 0
    }
    // `this` in here refers to the `StringLength` component
    this.inputChange = (e) => {
      this.setState({ // `this` in here is still the `StringLength` component
        inputLength: event.target.value.length
      });
    }
  }

  render () {
    return (
      <div>
      <input type='text'
             onChange={this.inputChange}/> // no longer needs to `bind`, since arrow functions have `this` permanently bound at time of creation
        {this.state.inputLength}
       </div>
    )
  }
}
```

In this example, `inputChange` is created inside `constructor`, guaranteeing that the `this` binding is pointing to the correct instance of `StringLength`. When we pass `inputChange` to `onChange`, we no longer need to bind. This approach also creates a new arrow function for every instance of `StringLength`, same as approach 2.

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

This approach, at first, might appear to be using the same `inputChange` callback for every instance of `StringLength`. So if you have 1000 `StringLength` instances on your page for some reason, you might think they all use the same `inputChange`, and being space efficient. The cold harsh truth is, every time `bind(this)` is called, a new function is created. According to official [MDN Documentation][mdn-bind], `bind()` returns a new function. `bind()` does not hold a reference to the old function, and simply is a brand new function in memory. Because of this, every time a new instance of `StringLength` is created, `render()` will create a new function `inputChange`, where `this` is appropriately bound. So with this in mind, we might as well use arrow functions. ¯\\\_(ツ)_/¯

## The Most Space Efficient Approach

```javascript
class StringLength extends React.Component { // same as original approach
  constructor () {
    super();
    this.state = {
      inputLength: 0
    }
  }

  inputChange (event) { // same as original approach
    this.setState({
      inputLength: event.target.value.length
    });
  }

  render () {
    // `this` in here returns the `StringLength` component
    return (
      <div>
      <input type='text'
           onChange={(event) => this.inputChange(event)}/> // `this` in the arrow function is the same as the surrounding environment, which is the correct `StringLength` component
        {this.state.inputLength}
       </div>
    )
  }
}
```

In this last approach, `onChange` is passed an arrow function. Since this arrow function was created in the `render` method, it still has the correct `this`. `this` in the arrow function is the same `this` as the `render` function, which is the correct `StringLength` instance. Of course, this still means that if we have 1000 different `StringLength` components, we will generate 1000 different arrow functions. But keep in mind that inside the arrow function `(event) => this.inputChange(event)`, `inputChange` is the same input change. So if inside `inputChange` is a ton of complicated logic, `inputChange` will not be duplicated (unlike the case of using `bind(this)`, which creates a new function in memory every time). This is the most space efficient approach, because the same `inputChange` in memory is used for every instance of `StringLength`.

[Home][home]

[home]: ../README.md
[mdn-bind]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind
