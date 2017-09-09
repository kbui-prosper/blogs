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
