# Binary Tree
Note: All are written in JavaScript

## Definition

```javascript
class TreeNode {
  constructor(val) {
    this.val = val;
    this.left = null;
    this.right = null;
  }
}
```

## Preorder Traversal

### Iterative Approach

```javascript
const preorderTraversal = (root) => {
    const output = [];
    const nodestack = [];
    if (root) nodestack.push(root);

    while (nodestack.length) {
        const { left, right, val } = nodestack.pop();
        output.push(val);

        if (right) nodestack.push(right);
        if (left) nodestack.push(left);
    }

    return output;
};
```

### Recursive Approach

```javascript
const preorderTraversal = (root) => {
    if (!root) return [];
    const { left, right, val } = root;
    return [val, ...preorderTraversal(left), ...preorderTraversal(right)];
};
```

## Inorder Traversal

### Interative Approach

```javascript
const inorderTraversal = (root) => {
    const output = [];
    const nodeStack = [];
    let current = root;

    while (current || nodeStack.length) {
        if (current) {
            nodeStack.push(current);
            current = current.left;
        } else {
            current = nodeStack.pop();
            output.push(current.val);
            current = current.right;
        }
    }

    return output;
};
```

### Recursive Approach

```javascript
const inorderTraversal = (root) => {
    if (!root) return [];
    const { left, right, val } = root;
    return [...inorderTraversal(left), val, ...inorderTraversal(right)];
};
```

## Postorder Traversal

### Iterative Approach

```javascript
const postorderTraversal = (root) => {
    if (!root) return [];

    const output = [];
    const inStack = [root];
    const outStack = [];

    while (inStack.length) {
        const { left, right } = node = inStack.pop();
        outStack.push(node);
        if (left) inStack.push(left);
        if (right) inStack.push(right);
    }

    while(outStack.length) {
        const { val } = outStack.pop();
        output.push(val);
    }

    return output;
};
```


### Recursive Approach

```javascript
const postorderTraversal = (root) => {
    if (!root) return [];
    const { left, right, val } = root;
    return [...postorderTraversal(left), ...postorderTraversal(right), val];
};
```
