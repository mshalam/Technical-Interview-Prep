
## Tree Traversal  
     
---
    
## Interviewer Prompt
 
Today you will write a series of iterator functions for trees.

- `breadthFirst`
- `depthFirstPreOrder`

(if time allows)

- `depthFirstPostOrder`

Each of these function will take a node of the tree and a callback. The function will iterate through the child nodes, calling the callback function on each of them. The difference between them is the order in which they iterate.

---

## Setup

```javascript
function node(value) {
  return {
    value,
    children: [],
  };
}
var a = node('a');
var b = node('b');
var c = node('c');
var d = node('d');
var e = node('e');
var f = node('f');
var g = node('g');
var h = node('h');
var i = node('i');
var j = node('j');
var k = node('k');
var l = node('l');
var m = node('m');

a.children.push(b, c, d);
b.children.push(e);
e.children.push(k, l);
c.children.push(f, g, h);
h.children.push(m);
d.children.push(i, j);
```

---

## Example

![tree](https://www.cpp.edu/~ftang/courses/CS241/notes/images/trees/tree1.bmp)

| Algorithm             | Order                       | Explanation                                                |
| --------------------- | --------------------------- | ---------------------------------------------------------- |
| `breadthFirst`        | `A B C D E F G H I J K L M` | Each "level" of the tree is printed in order               |
| `depthFirstPreOrder`  | `A B E K L C F G H M D I J` | Children nodes are visited before sibling nodes            |
| `depthFirstPostOrder` | `K L E B F G M H C I J D A` | A node is not traversed until all its children are reached |

---


## Interviewer Guide

---

### RE

- This differs from BST traversal in that each node may have any number of children

- You may need to remind your interviewee of what the different types of traversal mean

- Interviewee does not need to write the "node" function, but should be aware of the structure of a node

- Be sure to have your interviewee sketch an example tree

---

### AC

- Push a recursive solution

- Remind them that each child of a tree node is it's own tree

---

### TO

- If your interviewee finishes, ask them:
  - What is the Big O of the breadth first? Depth first?
  - Does your answer change if this becomes a binary search tree (max 2 children)?

---

## Solution Code (Breadth First)

```javascript
const breadthFirst = (startingNode, callback) => {
  // we use a queue to iterate over the tree
  // progressively adding the children as we go
  // The tree begins with the first node
  const queue = [startingNode];
  // you might want to consider handling edges cases
  /// such as not receiving a properly formatted node
  // or make a proper Node constructor/prototype (see below)
  while (queue.length) {
    // we shift off the array instead of iterating with a counter
    // as we are treating it as a queue (FIFO)
    const node = queue.shift();
    callback(node.value);
    // es6 format:
    queue.push(...node.children);
    // es5 might look like this if queue were a var (or let) instead of const
    // queue = queue.concat(node.children)
    // or:
    // queue.push.apply(queue, node.children)
  }
};
```

---

## Solution Code (Depth First)

```javascript
// depth first seems trivial in comparison! Simply log the value
// and then call the function on each node
const depthFirstPreOrder = (startingNode, callback) => {
  callback(startingNode.value);
  startingNode.children.forEach(child => {
    depthFirstPreOrder(child, callback);
  });
};

const depthFirstPostOrder = (startingNode, callback) => {
  startingNode.children.forEach(child => {
    depthFirstPostOrder(child, callback);
  });
  callback(startingNode.value);
};
```

---
 
## Summary

Big O

- Breadth First: O(n)
- Depth First: O(n)

[Video Solution](https://www.youtube.com/watch?v=4JPG-eRQpzY)

