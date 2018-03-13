# Binary Tree

## Definition

```javascript
class TreeNode {
  constructor(val) {
    this.val = val;
    this.left = null;
    this.right = null;
  }

  append(side, node) {
    this[side] = node;
  }

  appendLeft(node) {
    append('left', node);
  }

  appendRight(node) {
    append('right', node);
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
  let output = [val];

  if (left) output = [...output, ...preorderTraversal(left)]
  if (right) output = [...output, ...preorderTraversal(right)];

  return output;
};
```
