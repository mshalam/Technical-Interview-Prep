## Solving Graphs
  
---
 
## Interviewer Prompt

Write a function that determines if a path exists between two vertices of a directed graph.

The graph will be represented as an object, each of whose keys represents a vertex of the graph and whose value represents all vertices that can be reached from the aforementioned key.

In the example below, there is a connection from vertex a to vertex b and a connection from vertex b to vertices c and d but not a connection from vertex b to vertex a.

```javascript
{
  a: ['b'],
  b: ['c', 'd'],
  c: ['d']
}
```


---

## Example Output

Note: interviewee does not have to construct their own graphs.

.center[![graph](https://i.imgur.com/uqyXmfh.png)]

```javascript
const graph = {
  a: ['b'],
  b: ['c', 'd'],
  c: ['d'],
  d: []
}

doesPathExist(graph, 'a', 'b') // true
doesPathExist(graph, 'b', 'a') // false
doesPathExist(graph, 'a', 'd') // true
doesPathExist(graph, 'a', 'a') // false
```

---

## Example Output continued

.center[![graph](https://i.imgur.com/ehvb9qx.png)]

```javascript
const graph = {
  a: ['a', 'c'],
  c: ['r', 's'],
  r: ['a'],
  s: []
}

doesPathExist(graph, 'a', 'a') // true
doesPathExist(graph, 'c', 'c') // true
doesPathExist(graph, 'r', 's') // true
doesPathExist(graph, 's', 'a') // false
```
---

class: center middle
## Interviewer Guide

---

### RE

This is a good place to have your interviewee draw out the graph and think through how they want to walk through the nodes.

#### Guide:

* Interviewers: make it clear that the nodes in this graph are represented by strings like 'a' and 'b', not by instances of a Node class.

* If your interviewee continues without asking questions, stop them and ask, "Do you have any questions about what the graph looks like?" Your prompt mentions that the graph is directed, but they may not have caught on to this fact.

* Your interviewee may also not realize that we are allowing cyclic connections in these graphs. Again, this is easy to visualize/reason about if they have drawn out an example graph or two.

---

### AC

This problem is essentially a DFS/BFS problem. Either algorithm is sufficient. The only catch is that graphs can be cyclic. In other words, it's possible for a loop to exist in the graph.

#### Guide:

* You can let your interviewee come up with a solution that does not account for cycles, then let them refine it afterwards.

* See if they can come up with their own way of keeping track of visited nodes. If they are stuck, you can suggest that they use a separate object, either 'globally' or as another argument to their function.

---

### TO

Have your interviewee test their implementation on acyclic and cyclic graphs.

#### Guide:
* If your interviewee finishes, ask them:
  * What are the time and space complexities for their approach?

  * Can you think of alternative data structures for keeping track of visited nodes? (Sets and Maps are always good to know)

---

## Solution

```javascript
const doesPathExist = (graph, start, target, visited = {}) => {
  if (!graph[start]) return false
  visited[start] = true;

  return graph[start].some((vertex) => {
    if (vertex === target) return true;
    if (!visited[vertex]) {
      return doesPathExist(graph, vertex, target, visited);
    } else {
      return false;
    }
  });
}
```
[MDN .some()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/some)
---

## Big O

* Both DFS and BFS take O(V + E) time where V is the number of vertices or nodes and E is the number of edges. We MUST attempt to visit every node, which will take us through potentially many edges.

  * For acyclic graphs, we might visit every node and hit the leaves
  * For cyclic graphs, the cycle might not occur until a 'leaf'
  * For dense graphs, edges will dominate
  * For sparse graphs, vertices will dominate

* Keeping track of a 'visited' object means we need O(v) additional space

---
## Summary

* All trees are graphs. Techniques you learn to solve graphs can be applied for various kinds of trees, and often vice versa.

* You will often run into problems where you need to track 'visited' nodes
  * Sometimes this means keeping a table of visited nodes

  * If nodes are objects, this could mean adding a 'visited' property

---

## Discussion

The data structure seen ***earlier*** used to represent the graph is called an **adjacency list**. An alternative data structure exists for representing graphs called adjacency matrices. The cyclic graph above could have been modeled as ***follows*** using an **adjacency matrix**:

        a  c  s  r
      a 1  1  0  0
      c 0  0  1  1
      s 0  0  0  0
      r 1  0  0  0

In javascript, this table would be represented using an array of arrays or object of objects. A 1 indicates that a given vertex has an edge pointing to another vertex, and a 0 indicates that it does not. Both of these show direction!

---

## Discussion

Adjacency matrix:

        a  c  s  r
      a 1  1  0  0
      c 0  0  1  1
      s 0  0  0  0
      r 1  0  0  0

This table reads as follows:<br>

`a -> a`<br>
`a -> c`<br>
`c -> s`<br>
`c -> r`<br>
`r -> a`

---

## Discussion

Consider the tradeoffs between using one of these data structures or the other. Which do we prefer for the following operations?

* Testing if a given edge exists
  * Adjacency matrix O(1)
* Finding the # of edges of a vertex
  * Adjacency list O(1)
* Insertion/deletion of edges
  * AM O(1), AL O(d) where d is degree of vertex
* Memory usage for sparse graphs
  * AL O(v + e), AM O(v^2)
* Memory usage for dense graphs
  * AM O(v^2)
* Graph traversal
  * AL O(v + e), AM O(n^2)
* Better overall
  * Adjacency List


