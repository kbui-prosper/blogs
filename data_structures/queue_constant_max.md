# Building a queue that has constant time enqueue, constant time dequeue (amortized), and constant time access to the maximum (or minimum) value in the queue

This article aims to show the readers one way to build a queue data structure that can enqueue and dequeue while maintaining an API to access the maximum value in the queue in constant O(1) time.

## A stack can maintain a reliable history of maximum values

Intuitively, queues cannot maintain a history of maximum values, because of its first-in-first-out nature. However, a stack data structure, which is last-in-first-out, can easily maintain a history of maximum values.

#### Basic stack

Consider the following basic stack data structure:

```javascript
class Stack {
  constructor () {
    this.store = [];
  }

  push (val) {
    this.store.push(val);
  }

  pop () {
    return this.store.pop();
  }
}
```

This stack data structure can be used like this:

```javascript
myStack = new Stack;
myStack.push(4);
myStack.push(77);

console.log(myStack.store); // [4, 77]

myStack.pop(); // 77
console.log(myStack.store); // [4]
```

#### Stack with max history

We can rewrite our `Stack` class to maintain a `maxHistory`. Every time a new number is pushed into the stack, it will also be pushed to `maxHistory`. Every time a number is popped off the stack, it will be checked against the last item in `maxHistory`. If they are equal, the last item in `maxHistory` will be popped off. It can be implemented like so:

```javascript
class StackWithMax {
  constructor () {
    this.store = [];
    this.maxHistory = [];
  }

  push (val) {
    this.store.push(val);

    // check if maxHistory is empty
    // then check if the new `val` is the new max
    if (this.maxHistory.length === 0 ||
        val >= this.maxHistory[this.maxHistory.length - 1]) {
      this.maxHistory.push(val);
    }
  }

  pop () {
    const popped = this.store.pop();
    if (popped === this.maxHistory[this.maxHistory.length - 1]) {
      this.maxHistory.pop();
    }
    return popped;
  }

  max () {
    return this.maxHistory[this.maxHistory.length - 1];
  }
}
```

The `StackWithMax` can be used like so:

```javascript
awesomeStack = new StackWithMax;
awesomeStack.push(99);
awesomeStack.push(1);
awesomeStack.push(50);
awesomeStack.push(100);

console.log(awesomeStack.store); // [99, 1, 50, 100]
console.log(awesomeStack.maxHistory); // [99, 100]
console.log(awesomeStack.max()); // 100

awesomeStack.pop();
console.log(awesomeStack.store); // [99, 1, 50]
console.log(awesomeStack.maxHistory); // [99]
console.log(awesomeStack.max()); // 99

awesomeStack.pop();
console.log(awesomeStack.store); // [99, 1]
console.log(awesomeStack.maxHistory); // [99]
console.log(awesomeStack.max()); // 99
```

## A queue using 2 stacks

Okay fine making a stack that maintains a `maxHistory` is too easy. What about a queue that always has constant access to the max? We can achieve this by making a queue using only stacks. Let's first construct a basic queue using basic stacks.

#### Basic queue using 2 basic stacks

The following code uses the first `Stack` class:

```javascript
class Queue {
  constructor () {
    this.inStack = new Stack;
    this.outStack = new Stack;
  }

  enqueue (val) {
    this.inStack.push(val);
  }

  dequeue () {
    if (this.outStack.store.length === 0) {
      this.fillOutStack();
    }
    return this.outStack.pop();
  }

  fillOutStack () {
    while (this.inStack.store.length !== 0) {
      this.outStack.push(this.inStack.pop());
    }
  }
}
```

This basic `Queue` can be used like so:

```javascript
queue = new Queue;

queue.enqueue(1);
queue.enqueue(2);
queue.enqueue(3);
queue.dequeue(); // 1
queue.dequeue(); // 2
queue.enqueue(9);
queue.enqueue(10);
queue.dequeue(); // 3
queue.dequeue(); // 9
```

#### A queue using `StackWithMax`

We can rewrite the same `Queue` above, but this time using `StackWithMax`. This gives us access to 2 max values using the `StackWithMax.prototype.max` method, one from the `inStack`, and one from the `outStack`. Then all we have to access the max of the queue is to compare these 2 max values, and that operation is constant.

```javascript
class QueueWithMax {
  constructor () {
    this.inStack = new StackWithMax;
    this.outStack = new StackWithMax;
  }

  enqueue (val) {
    this.inStack.push(val);
  }

  dequeue () {
    if (this.outStack.store.length === 0) {
      this.fillOutStack();
    }
    return this.outStack.pop();
  }

  fillOutStack () {
    while (this.inStack.store.length !== 0) {
      this.outStack.push(this.inStack.pop());
    }
  }

  max () {
    if (this.inStack.max()) {
      if (!this.outStack.max() ||
          this.inStack.max() > this.outStack.max()) {
        return this.inStack.max();
      } else {
        return this.outStack.max();
      }
    } else {
      return this.outStack.max();
    }
  }
}
```

This queue can be used like so:

```javascript
queue = new QueueWithMax;

queue.enqueue(1);
queue.enqueue(2);
queue.enqueue(3);

console.log(queue.max()); // 3

queue.dequeue(); // 1
queue.dequeue(); // 2

console.log(queue.max()); // 3

queue.enqueue(9);
queue.enqueue(10);

console.log(queue.max()); // 10

queue.dequeue(); // 3
queue.dequeue(); // 9

console.log(queue.max()); // 10
```

If you want to also keep track of the min, simply add a `minHistory` stack to the `Stack` class.

[Home][home]

[home]: ../README.md
