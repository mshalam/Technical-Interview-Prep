## Priority Queue

---  
   
## Definitions  
 
A *queue* is a data structure that stores pieces of data and returns them in the same order in which they were inserted.  One way of implementing them is as a linked list.

A *priority queue* is a data structure that takes a priority value with each piece of data and returns the data in order of priority.

---

## Interviewer Prompt

Implement a priority queue with the following 3 methods:

`insert(data, priority)` // inserts data into the priority queue with the given priority

`peek()` // returns the value of the item with the highest priority without removing it from the priority queue

`popMax()` // returns the value of the item with the highest priority and also removes it from the priority queue

For the purposes of our implementation, "higher priority" corresponds to a higher integer value. Note, however, that this could be implemented either way.

---

## Example Output

A hospital emergency room could use a priority queue to determine the order in which their doctors see patients.  The priority queue would work the following way:

```javascript
const pq = new PriorityQueue();
pq.insert('Jill, concussion', 7);
pq.insert('John, stomach pain', 5);
pq.peek() // ==> 'Jill, concussion'
pq.peek() // ==> 'Jill, concussion'  // Jill is still in the PQ
pq.insert('Dave, sprained ankle', 1);
pq.insert('Bob, breathing problems', 8)
pq.peek() // ==> 'Bob, breathing problems'
pq.popMax() // ==> 'Bob, breathing problems'
pq.peek() // ==> 'Jill, concussion' // Bob has been removed from the PQ
```

---

## Approach

A naive approach would be to have what is essentially a linked list where each node also has a priority. To insert, simply traverse until you reach a node that is lesser in priority, and put the new node just before that one.

---

## Code

---

### Internals

```javascript
function Node (data, priority) {
  this.data = data;
  this.priority = priority;
  this.next = null;
}

function PriorityQueue () {
  this.first = null;
}
```

---

### API

```javascript
PriorityQueue.prototype.insert = function (data, priority) {
  const newItem = new Node(data, priority);
  if (!this.first || this.first.priority < priority) {
    // First case: Check if the PQ is empty, or newItem's priority > this.first's
    newItem.next = this.first;
    this.first = newItem; // New Node (newItem) becomes new first
  } else {
    // Second case: Find where to insert newItem
    let currentNode = this.first;
    while (currentNode.next && currentNode.next.priority >= priority) {
      // Traverse queue until it finds a node with priority < search priority
      currentNode = currentNode.next;
    }
    // Here, currentNode is right before where you want to insert newItem. Point
    // newItem.next to currentNode.next, then point currentNode's next to newItem.
    newItem.next = currentNode.next;
    currentNode.next = newItem;
  }
}

PriorityQueue.prototype.peek = function () {
  return this.first.data;
}

PriorityQueue.prototype.popMax = function () {
  const maxVal = this.first;
  this.first = this.first.next;
  return maxVal.data;
}
```

---

## Complexity

This should be `O(1)` time complexity for peek and popMax, but `O(n)` time complexity for insert (where `n` is the number of nodes in the priority queue).

Space complexity is `O(n)`, where `n` is the number of items in the queue.

---

## Optimization: Binary heap

A more optimal solution could involve a heap, in particular a binary max heap. This could give us `O(log n)` insertion time, with the not-so-bad down-side of changing `popMax` time to `O(log n)`. Space complexity remains the same at `O(n)` since we still store a node for each item in the queue, just in a tree (heap) arrangement.

---

## Binary Heaps

A **binary heap** has 2 properties:

1. Uses a *complete* binary tree, which means that every level of the tree is full except the bottom level, which is filled from left to right
2. Insertions satisfy the *heap-order-property*, which says that no child's value is greater than its parent's value (in a *max* heap)

![](https://upload.wikimedia.org/wikipedia/commons/thumb/3/38/Max-Heap.svg/240px-Max-Heap.svg.png)

---

### Data storage

Because binary heaps are complete binary trees, they're often stored as arrays in an order that follows a level ordered traversal starting at the root. We could use a pointer-and-node-based (linked) data structure for the heap implementation, but the array runs faster because we don't have to set and reset references from one node to another.

![](https://upload.wikimedia.org/wikipedia/commons/8/86/Binary_tree_in_array.svg)

---

## Math!

By mapping objects to indices in this way, we can easily get an object's parent or children based on its index.

If `i` is the index of an object in the array (starting at 0) we can use the following formulas:
- parent is (`i`-1)/2 rounded down
- left child is 2`i`+1
- right child is 2`i`+2

---

### Max Heap vs. Min Heap

Note that this is a **max heap**, in which the maximum-priority element ends up at the root of the heap. This is in contrast with a **min heap**, in which the minimum-priority element ends up at the root of the heap. Consider the tradeoffs in either variety for `popMax()` and `popMin()`.

---

## Heap Approach

---

### Inserting an item

Because we're working with a complete binary tree we insert at the bottom level at the first free spot from the left - push it into our array. In the case that a new insertion violates the heap-order-property we need to re-heapify. This is done by comparing the insertion's priority with its parent's priority and swapping the objects if the insertion's priority is greater - repeat this process until the insertion's priority is not greater than its parent's.

![](https://upload.wikimedia.org/wikipedia/commons/a/ac/Heap_add_step1.svg)
![](https://upload.wikimedia.org/wikipedia/commons/1/16/Heap_add_step2.svg)
![](https://upload.wikimedia.org/wikipedia/commons/5/51/Heap_add_step3.svg)

---

### Viewing the highest-priority item

Returning the maximum-priority value is easy since the heap-order-property guarantees that the maximum priority will always be at the root of the tree.

![](https://upload.wikimedia.org/wikipedia/commons/1/1c/Heap_delete_step0.svg)

---

### Dequeue

`popMax` is done by removing the root object and replacing it with the last entry in the tree. This almost always ends up violating the heap-order-property so we need to re-heapify. This is done by comparing the root/parent's priority with its children's priorities. A swap should happen if the parent's priority is less than one or both of its children's priorities and should be done with the child holding the greater priority. Repeat this process until the parent's priority is not less than either of its children's priorities.

![](https://upload.wikimedia.org/wikipedia/commons/1/1c/Heap_delete_step0.svg)
![](https://upload.wikimedia.org/wikipedia/commons/e/ee/Heap_remove_step1.svg)
![](https://upload.wikimedia.org/wikipedia/commons/2/22/Heap_remove_step2.svg)

---

## Code

---

### Utility methods

```javascript
class HeapPQ {
  constructor () {
    this._items = [];
  }

  _swap (childIdx, parentIdx) {
    [this._items[childIdx], this._items[parentIdx]] =
      [this._items[parentIdx], this._items[childIdx]];
  }

  _parentIdx (childIdx) {
    return Math.floor((childIdx - 1) / 2);
  }

  _childrenIndices (parentIdx) {
    return [parentIdx * 2 + 1, parentIdx * 2 + 2];
  }

  _priority (i) {
    return this._items[i].priority;
  }
```

---

### insert

```javascript
  insert (data, priority) {
    this._items.push({data, priority});
    this._heapifyUp();
  }

  _heapifyUp () {
    let currentIdx = this._items.length - 1;
    while (currentIdx > 0 &&
        this._items[currentIdx].priority > 
        this._items[this._parentIdx(currentIdx)].priority) {
      this._swap(currentIdx, this._parentIdx(currentIdx));
      currentIdx = this._parentIdx(currentIdx);
    }
  }
```

---

### peek

```javascript
  peek () {
    return this._items[0].data;
  }
```

---

### popMax

```javascript
  popMax () {
    const max = this._items[0];

    // replace the root with the last item in the collection
    this._items[0] = this._items.pop();

    this._heapifyDown();
    return max.data;
  }

  _heapifyDown () {
    let currentIdx = 0;
    let [left, right] = this._childrenIndices(currentIdx);
    let idxLarger;
    const length = this._items.length;
    while (left < length) {
      if (right < length) {
        idxLarger = this._priority(left) >= this._priority(right) ? left : right;
      }
      else idxLarger = left;

      if (this._priority(currentIdx) < this._priority(idxLarger)) {
        this._swap(idxLarger, currentIdx);
        currentIdx = idxLarger;
        [left, right] = this._childrenIndices(currentIdx);
      }
      else return;
    }
  }
}
```

---

## Interviewer Guide

Remember that this week's theme is **data structures**, so make sure the interviewee talks about the data structure they're using before moving on from the "Approach" step.

If they go right for the linked-list implementation, you can let them code it out, but make sure to ask about performance and prompt them to think of other data structures that might be more optimal. If you go this route, leave 5-10 minutes at the end to at least discuss how a heap would work in an abstract sense.

---

## Further Reading

- Taken from [Princeton's Algorithms 4th Edition](http://algs4.cs.princeton.edu/24pq/):

![](http://algs4.cs.princeton.edu/24pq/images/heap-representations.png)

- [Stanford's CS106B PQueue explainer](http://web.stanford.edu/class/archive/cs/cs106b/cs106b.1174/handouts/190%20Assignment%205.pdf) -- binary heap explanation starts on pg. 7

- [CMU explainer](https://www.cs.cmu.edu/~adamchik/15-121/lectures/Binary%20Heaps/heaps.html)

