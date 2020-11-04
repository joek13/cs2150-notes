# HeapsHuffman
# Notes 11.02.2020 - notes

# Heaps (Priority Queues) and the Huffman Coding

### Motivation
- "There are times when some things are more important than others."
  - Operating system scheduler
  - Bandwidth on network router
    - Limited bandwidth, but want to prioritize some traffic
    - VoIP gets prioritized

### Priority Queue ADT
- Operations
  - insert
    - with *priority*
  - findMin
    - finds the minimum element
  - deleteMin
    - finds, returns, and removes the minimum element

**Potential realizers:**
- Unsorted array
- Unsorted linked list
- Sorted array
- Sorted linked list
- Binary search tree
- AVL or red-black tree
- Hashtable

Desideratum:
- constant-time `findMin`

**Binary Heap**: *one* way to implement a priority queue
Binary heap is a binary tree (but not a BST), with a different:
- Structure property
- Ordering property

A *perfect* binary tree has all leaf nodes at the same depth, all internal nodes have 2 children
Removing an arbitrary node from a perfect binary tree does not preserve its perfection

A *full* binary tree is a binary tree in which every node has exactly zero or two children.
  (No restrictions about the level of children, just restrictions on whether

A binary heap is an *almost complete* binary tree, which is a binary tree that is completely filled, except possibly the bottom level, which is filled left to right.

Almost complete binary trees can be stored in an array

From node *i*:
- Left child: 2*i*
- Right child: 2*i* + 1
- Parent: floor(*i*/2)

All of these indices can be computed readily in assembly (all left/right shifts and increments)

*Even though we will diagram them with trees, we will almost always back them with arrays*

### Heap ordering property
For every non-root node X, the key in the parent of X is less than (or equal to) the key in X. The tree is *partially* ordered.

So the min is always at the root

`findMin` is constant -- just look at the root node
`insert` -- "percolate" up
`deleteMin` -- "percolate" down

#### Insert
- Insert at rightmost of bottom row -- in the array, first empty spot
- Repeatedly compare with parent, swap if misordered
- Worst-case: `logn` runtime
- Expected runtime: *constant*
  - Half of the nodes are leaves, so half of the inserts will only move up one level
  - Quarter are one level above leaves, so a quarter of inserts will only move up two levels
  - One eighth require moving up three levels
  - etc...
  - 1/2 * 1 + 1/4 * 2 + 1/8 * 3 + … = 2
  - **Important:** This is not amortized: if you're always inserting a minimum, you'll always trigger worst case

#### Delete
- Remove root node, take rightmost leaf and place it at root
- Repeatedly replace new root node with smaller of its children
- Worst-case: `logn` runtime
- We always need to swap with the smaller of the two children:
  - Consider:
```
   25    <--- Needs to be percolated!
  /  \
 10  20

   20
  /  \
 10  25  <--- If we swap with greater child, 20 > 10, and the ordering property is violated.
```


## Other heap operations
`decreaseKey(processID, amount)`: decrease priority value, making a process more important, percolate up
`increaseKey(processID, amount)`: increase priority value, making a process less important, percolate down
`remove(processID)`: remove a process, move to top, then delete
  - `decreaseKey(processID, -infinity)`
  - `deleteMin()`

Worst case runtime for all of these is `Theta(n)` because we have to search and find the element with a given `processID`
(We can optimize this, because perhaps process records have pointers to their entries in the binary heap)

## Heapsort
- Insert `n` elements, then remove `n` elements
- Each one has an insertion time of `logn`
- And then removal time of `logn`
- Therefore `Theta(nlogn)`
- But this is an unstable sort, so it's not used as often as mergesort
