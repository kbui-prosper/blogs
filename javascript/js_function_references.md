# Mini-Proof that Functions are Passed by Reference in Javascript

## Functions Are Objects, and Objects are Passed by Reference

Input the following code into your favorite Javascript engine:

```javascript
const person1 = {
  name: 'John Smith',
  age: 30,
  rebirth() {
    this.age = 0;
  }
}

const rebirth = person1.rebirth;
```

Now, when you inspect `rebirth` and `person1.rebirth` in your javascript REPL, you will see that they return the same values.

```javascript
console.log(rebirth); // ƒ rebirth() { this.age = 0; }
console.log(person1.rebirth); //ƒ rebirth() { this.age = 0; }
```

Similarly, when you inspect the `name` attribute of these variables, you will get the same result:

```javascript
  console.log(rebirth.name); // 'rebirth'
  console.log(person1.rebirth.name); // 'rebirth'
```

When I modify the name attribute of one variable, it modifies the name attribute of both variables:

```javascript
  rebirth.someAttr = 'whatever string';
  console.log(rebirth.someAttr); // 'whatever string'
  console.log(person1.rebirth.someAttr); // 'whatever string'
```

As you can see, modifying one variable affects all variables. That is because when a variable is assigned a function object, only a reference is passed. The 2 variables `rebirth` and `person1.rebirth` both point to the same function object.
