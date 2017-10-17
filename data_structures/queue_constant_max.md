# Article In Progress, please visit [home page][home] for other articles

# Building a queue that has constant time access O(1) to the maximum (or minimum) value in the queue

This article aims to show the readers one way to build a queue data structure that can enqueue and dequeue while maintaining an API to access the maximum value in the queue in constant O(1) time.

## A stack can maintain a reliable history of maximum values

Intuitively, queues cannot maintain a history of maximum values, because of its first-in-first-out nature. However, a stack data structure, which is last-in-first-out, can easily maintain a history of maximum values. Consider the following basic stack data structure:

```javascript
class Stack {
  constructor () {
    this.store = [];
  }

  push (val) {
    this.store.push(val);
  }

  pop (val) {
    return this.store.pop();
  }
}
```



[Home][home]

[home]: ../README.md
