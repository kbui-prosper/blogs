# The reason why you have to `bind(this)` for callback functions in React

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
}```

Most of the things going on here is beyond the scope of this analysis. The key takeaway here is that `this.inputChange` is passed into the React element as part of a POJO (Plain Old Javascript Object). Specifically, this object:
```javascript
{
  type: 'text',
  onChange: this.inputChange.bind(this)
}
```
This approach assigns the `inputChange` method to the `onChange` key of this object, and passes this object into `React.createElement`. Because of this, when this method is invoked within the React element, the `this` binding is lost.

## The ambitious but naive work-around: Arrow Functions

We developers naturally shy away from repeatedly typing the same code again and again. In the case of React, whenever an event handler, for example, `inputChange`, is passed into a React element/component, we have to do `.bind(this)`. This leads to multiple occurrences of the code `.bind(this)` in React apps, which doesn't appear very DRY. Intuitively, arrow functions come to mind when finding a solution DRY up our code. Before reading on about analysis of the arrow function approach, make sure you take a look at [this analysis][js-arrow-this] of arrow functions.

[js-arrow-this]: ../javascript/arrow_and_this.md

Consider this approach:

```javascript
StringLength.prototype.inputChange = (e) => {
  this.setState({
    inputLength: event.target.value.length
  });
}
```
