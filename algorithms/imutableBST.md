
# Prompt
     
Implement an immutable binary search tree class. The class constructor should accept (at least) a single argument which will represent the value for its root node. Each instance should have two methods: `insert`, which takes a numerical value and returns a new binary search tree containing that value, and `contains`, which takes a numerical value and returns `true` or `false` based on whether the tree contains it.

Insert should not mutate the existing binary search tree. That is:

```js
const bstA = new ImmutableBST(5);
const bstB = bstA.insert(6);
console.log(bstA); // contains only 5, NOT 6
console.log(bstB); // contains 5 and 6
```

Follow-up: build a `remove` method, which takes a value and returns a new binary search tree that does not include that value.

Read more here: https://en.wikipedia.org/wiki/Persistent_data_structure#Trees and/or here: https://medium.com/@dtinth/immutable-js-persistent-data-structures-and-structural-sharing-6d163fbd73d2#.7xy1ccvr0.

# Examples

```js
const bst = new ImmutableBST(5);
const bstMore = bst.insert(4).insert(3).insert(1).insert(10).insert(11).insert(15).insert(2).insert(100);
bstMore.contains(5); // true
bstMore.contains(3); // true
bstMore.contains(11); // true
bstMore.contains(12); // false
console.log(bst === bstMore); // false, because of immutability
```

# Solution (Constructor Function)

```js
// O(1) creation time
function ImmutableBST (value, left, right) {
  this.value = value;
  this.left = left || null;
  this.right = right || null;
  this.size = 1 + (left && left.size || 0) + (right && right.size || 0);
}
// O(log n) insertion time
ImmutableBST.prototype.insert = function (value) {
  const newValue = this.value;
  let newLeft, newRight;
  if (value <= this.value) {
    newRight = this.right;
    // clone the left node with the value inserted
    // or create a fresh left node if one does not already exist
    newLeft = (this.left ? this.left.insert(value) : new ImmutableBST(value));
  } else {
    newLeft = this.left;
    // clone the right node with the value inserted
    // or create a fresh right node if one does not already exist
    newRight = (this.right ? this.right.insert(value) : new ImmutableBST(value));
  }
  // clone this node with the same value and either a new left child or a new right child (depending on above)
  return new ImmutableBST(newValue, newLeft, newRight);
};
// O(log n) retrieval time
ImmutableBST.prototype.contains = function (value) {
  if (this.value === value) return true;
  if (value < this.value) {
    return Boolean(this.left) && this.left.contains(value);
  } else {
    return Boolean(this.right) && this.right.contains(value);
  }
};
ImmutableBST.prototype.min = function () {
  if (!this.left) return this.value;
  else return this.left.min();
};
ImmutableBST.prototype.max = function () {
  if (!this.right) return this.value;
  else return this.right.max();
};
// O(log n) removal time
ImmutableBST.prototype.remove = function (value) {
  if (this.value === value) {
    // if we have matched, distinguish between three different cases
    if (this.left && this.right) { // case 1: the node has both children
      let newValue, newLeft, newRight;
      // remove a value from the "fuller" child
      // we will use that value as the value for our new node
      if (this.left.size > this.right.size) {
        newRight = this.right;
        // get the largest of the smaller child
        newValue = this.left.max();
        newLeft = this.left.remove(newValue);
      } else {
        newLeft = this.left;
        // get the smallest of the larger child
        newValue = this.right.min();
        newRight = this.right.remove(newValue);
      }
      return new ImmutableBST(newValue, newLeft, newRight);
    } else if (!this.left && !this.right) { // case 2: the node has no children
      // easy, if a node has no children its parent should replace it with null
      return null;
    } else { // case 3: the node has one child
      // also easy, if a node has one child, its parent should replace it with that child
      return this.left || this.right;
    }
  } else {
    // we have not yet found the given value to remove, continue recursing
    const newValue = this.value;
    let newLeft, newRight;
    if (value < this.value) {
      newRight = this.right;
      // clone the left node with the value removed
      // or if there is no left node, stop recursing
      newLeft = (this.left ? this.left.remove(value) : null);
    } else {
      newLeft = this.left;
      // clone the right node with the value removed
      // or if there is no right node, stop recursing
      newRight = (this.right ? this.right.remove(value) : null);
    }
    // clone this node with the same value and either a new left child or a new right child (depending on above)
    return new ImmutableBST(newValue, newLeft, newRight);
  }
};
```

# Solution (ES6 Class)
```js
// O(1) creation time
class ImmutableBST {
  constructor(value, left, right){
    this.value = value;
    this.left = left || null;
    this.right = right || null;
    this.size = 1 + (left && left.size || 0) + (right && right.size || 0);
  }

  // O(log n) insertion time
  insert (value) {
    const newValue = this.value;
    let newLeft, newRight;
    if (value <= this.value) {
      newRight = this.right;
      // clone the left node with the value inserted
      // or create a fresh left node if one does not already exist
      newLeft = (this.left ? this.left.insert(value) : new ImmutableBST(value));
    } else {
      newLeft = this.left;
      // clone the right node with the value inserted
      // or create a fresh right node if one does not already exist
      newRight = (this.right ? this.right.insert(value) : new ImmutableBST(value));
    }
    // clone this node with the same value and either a new left child or a new right child (depending on above)
    return new ImmutableBST(newValue, newLeft, newRight);
  };

  // O(log n) retrieval time
  contains (value) {
    if (this.value === value) return true;
    if (value < this.value) {
      return Boolean(this.left) && this.left.contains(value);
    } else {
      return Boolean(this.right) && this.right.contains(value);
    }
  };

  min () {
    if (!this.left) return this.value;
    else return this.left.min();
  };

  max () {
    if (!this.right) return this.value;
    else return this.right.max();
  };

  // O(log n) removal time
  remove (value) {
    if (this.value === value) {
      // if we have matched, distinguish between three different cases
      if (this.left && this.right) { // case 1: the node has both children
        let newValue, newLeft, newRight;
        // remove a value from the "fuller" child
        // we will use that value as the value for our new node
        if (this.left.size > this.right.size) {
          newRight = this.right;
          // get the largest of the smaller child
          newValue = this.left.max();
          newLeft = this.left.remove(newValue);
        } else {
          newLeft = this.left;
          // get the smallest of the larger child
          newValue = this.right.min();
          newRight = this.right.remove(newValue);
        }
        return new ImmutableBST(newValue, newLeft, newRight);
      } else if (!this.left && !this.right) { // case 2: the node has no children
        // easy, if a node has no children its parent should replace it with null
        return null;
      } else { // case 3: the node has one child
        // also easy, if a node has one child, its parent should replace it with that child
        return this.left || this.right;
      }
    } else {
      // we have not yet found the given value to remove, continue recursing
      const newValue = this.value;
      let newLeft, newRight;
      if (value < this.value) {
        newRight = this.right;
        // clone the left node with the value removed
        // or if there is no left node, stop recursing
        newLeft = (this.left ? this.left.remove(value) : null);
      } else {
        newLeft = this.left;
        // clone the right node with the value removed
        // or if there is no right node, stop recursing
        newRight = (this.right ? this.right.remove(value) : null);
      }
      // clone this node with the same value and either a new left child or a new right child (depending on above)
      return new ImmutableBST(newValue, newLeft, newRight);
    }
  };
}

let myBSTBase = new ImmutableBST(5) // contains 5
let myBSTBase2 = myBSTBase.insert(10) // contains 5, 10
let myBSTBase3 = myBSTBase.insert(15).insert(7) // contains 5, 15, 7
let buildingOnABase = myBSTBase3.insert(12) // contains 5, 15, 7, 12
```

