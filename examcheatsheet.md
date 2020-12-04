## CS2150 Final Exam Review Sheet
------

It's happening.

## Table of contents

Here are the sections of this document:

0. [Table of conents](#table-of-contents)
1. [Data structures](#data-structures)

## Data structures
------
### Collections
#### Arrays
Arrays Store some number of elements as a contiguous block of memory. Arrays are declared with an initial size (can be determined statically or dynamically), and they cannot be resized after creation.
##### Operations
| Operation | Complexity | Notes                         |
|-----------|------------|-------------------------------|
| Access    | Θ(1)       |                               |
| Search    | Θ(*n*)     | General case: unsorted array. |
| Insert    | Θ(*n*)     | Requires new array.           |
| Delete    | Θ(*n*)     | Requires new array.           |

##### Advantages
- Can often be statically allocated, avoiding the overhead of dynamic allocation.
    - (Even if the array is allocated dynamically, we get one allocation for the entire array instead of per element. See linked list disadvantages.)
- Can take advantage of spatial locality. Subsequent elements are stored contiguously in memory, so reading subsequent elements in an array will typically work well with cache.
- Calculating element addresses is easy, so accesses are worst-case constant time.
##### Disadvantages
- C arrays don't perform bounds checking, so accessing `arr[5]` where `arr` is a 3-element array won't cause a compile-time error. At runtime, it will either segfault or access some random value in memory.
- Cannot be resized.
- When passed as an argument to a function, we have to pass a second argument with the array's size. (This is because the array *decays* into a pointer to its first element.)
- Cannot be copied using `arrB = arrA`; has to be done using a for loop (or library function).
- Cannot be compared using `==`, have to compare element-by-element (or library function).
#### Linked Lists
Node-based datastructure where each node contains a value, and a pointer to the next node. (A doubly linked list node also contains a pointer to the previous node.) The list itself is kept with pointers to the head and tail nodes.
##### Operations
Worst-case runtime:
| Operation | Complexity | Notes                |
|-----------|------------|----------------------|
| Access    | Θ(*n*)     |                      |
| Search    | Θ(*n*)     |                      |
| Insert    | Θ(1)       | Insert at head/tail. |
| Delete    | Θ(1)       | Delete at head/tail. |

##### Advantages
- Guaranteed constant-time inserts at head/tail.
    - Doubly-linked lists guarantee constant-time inserts/deletions in the middle of the list

##### Disadvantages
- Takes more space than equivalent array because it has to store pointers
- Requires dynamic allocation and its concomitant overhead
- Lacks spatial locality, and accesses requires many pointer dereferences

#### Vectors
C++ STL type. Import using `#include <vector>`. An automatically-resizing collection, a lot like Java's `ArrayList`. It's backed by an array that is automatically grown to fit its contents.

##### Operations
Worst-case runtime:
| Operation | Complexity                                             | Notes |
|-----------|--------------------------------------------------------|-------|
| Access    | Θ(1)                                                   |       |
| Search    | Θ(*n*)                                                 |       |
| Insert    | Θ(*n*) worst-case; Θ(1) amortized if inserting at back |       |
| Delete    | Θ(*n*)                                                 |       |
##### Advantages
- Unlike an array, does bounds checking at runtime.
- Keeps track of its own size, so you don't have to pass that to a function.
- Can be copied using `=`
- Can be compared for equality using `==`
- Like array, provides worst-case constant time accesses.

##### Disadvantages
- Insertion will, on occasion, require a full copy of the elements, a linear-time operation. This operation is also Θ(n) in space complexity.
- Insertion or removal in the middle of a vector is always a linear-time operation.
#### Stacks (ADT)
LIFO: Last In, First Out collection. List of elements where only the most recently inserted element (the "top") can be removed.

Potential realizers: array, singly linked list, vector

##### Operations
**Note:** Complexity is given for typical, array-based implementation.
Worst-case runtime:
| Operation | Complexity | Notes                                         |
|-----------|------------|-----------------------------------------------|
| Peek      | Θ(1)       | Retrieve, but do not remove, at top of stack. |
| Push      | Θ(1)       | Add at top of stack.                          |
| Pop       | Θ(1)       | Remove at top of stack.                       |

##### Advantages
- Guaranteed constant time for all operations.
- LIFO model is sensible for many applications. Call stack, parenthesis balancing, postfix calculators, undo button

##### Disadvantages
- Can only interact with stack at its top. Messing with the middle is strictly forbidden.

#### Queues (ADT)
FIFO: First In, First Out collection. The least recently inserted element is the only element that can be removed.

Potential realizers: linked list, array, vector

##### Operations
**Note**: complexity is given for a typical linked list implementation of a queue.

| Operation | Complexity | Notes                    |
|-----------|------------|--------------------------|
| Peek      | Θ(1)       | Peeks at head.           |
| Enqueue   | Θ(1)       | Adds element at tail.    |
| Dequeue   | Θ(1)       | Removes element at head. |

#### Hashtables
Highly efficient key-value datastructure that, when certain preconditions are met, can usually nearly guarantee constant search, insert, and delete runtime complexities.

##### Operations
Worst-case complexities:
| Operation | Complexity | Notes |
|-----------|------------|-------|
| Find      | Θ(n)       |       |
| Insert    | Θ(n)       |       |
| Remove    | Θ(n)       |       |

##### Advantages
- Can almost guarantee constant average-case runtimes for find, insert, and delete.
##### Disadvantages
- "Wastes" space if load factor is low
- Fails to meet constant-time ideal if hash function isn't uniform and independent

### Tree-Based Collections
#### Binary Search Trees
A binary search tree is a binary tree that satisfies an additional ordering property: for any node x, its left child (if it exists) has a value less than x's value, and its right child (if it exists) has a value greater than x's value.

##### Operations
Worst-case runtime complexity:
| Operation | Complexity | Notes |
|-----------|------------|-------|
| Find      | Θ(n)       |       |
| Insert    | Θ(n)       |       |
| Remove    | Θ(n)       |       |

##### Advantages
- Typically offers logarithmic find, insert, and remove (when approximately balanced)
- Imposes an ordering on its elements
##### Disadvantages
- Performance is heavily dependent on order of insertion. If the elements are inserted in ascending order, the tree's operations are guaranteed to be worst-case, as the tree is heavily unbalanced.

#### Heaps
Binary tree with added heap ordering and structure properties.

**Structure**: all internal nodes have both left and right children. The bottom level is filled left-to-right.

**Heap Ordering**: *(min-heap)* for any node *x*, *x*'s parent has a value less than or equal to that of *x*.

Heaps provide a very high-performance implementation of the priority queue ADT.

Heaps are defined as trees, but are typically stored using a contiguous array. In array form, if the root is stored at index 1, we have the following relations for the `i`th node.

- Index of left child: `2 * i`
- Index of right child: `2 * i + 1`
- Index of parent: `floor(i / 2)`

##### Operations
Worst-case runtime:
| Operation | Complexity                         | Notes                                                |
|-----------|------------------------------------|------------------------------------------------------|
| FindMin   | Θ(1)                               |                                                      |
| Insert    | Θ(log n) worst-case; Θ(1) expected | Expected runtime is for randomly distributed inserts |
| RemoveMin | Θ(log n)                           |                                                      |

###### Advantages
- Expected insert runtime is actually constant, as long as inserts are randomly distributed
    - This is not *amortized*; inserting new minimums will always trigger the worst case.

##### Disadvantages
- Inserting minimum values always triggers worst case

#### AVL Trees
Self-balancing binary search tree. Each node has an attached balance factor, which is exactly the difference in height between the right and left subtrees. When the balance factor equals 2 or -2, the appropriate rotations are performed to rebalance the tree.

##### Operations
Worst-case runtime:
| Operation | Complexity | Notes |
|-----------|------------|-------|
| Find      | Θ(log n)   |       |
| Insert    | Θ(log n)   |       |
| Remove    | Θ(log n)   |       |

##### Advantages
- Self-balancing means that worst-case is better than linear

##### Disadvantages
- Balancing after insertion adds extra (constant) overhead
- Slower in practice than red-black trees

#### Red-Black Trees
Self-balancing binary search tree. Each node has an attached color, either "red" or "black." The root is black, all leaves are black, and both children of any red node are black. There are several more properties I don't feel like listing out. (Call it a pedagogical decision.)

##### Operations
Worst-case runtime:
| Operation | Complexity | Notes |
|-----------|------------|-------|
| Find      | Θ(log n)   |       |
| Insert    | Θ(log n)   |       |
| Remove    | Θ(log n)   |       |

##### Advantages
- Doesn't balance as often, so more efficient than AVL trees (although this doesn't show up in the runtime analysis).

##### Disadvantages
- A pain to implement