# Queues
# Notes 09.15.2020 - notes

## Queue 
- FIFO, first-in, first-out
- Isomorphic to a list, but inserts only happen at one end and removals only happen at the other end
- Operations
  - Enqueue: insert at back
  - Dequeue: delete at other end
  - *fifo*: first in, first out
- Constant time for all operations
  - (minor disclaimer about a full vector queue)

## Queue applications
- Print queue
  - For a printer
- Lines in general
- File serving
- Call queues in tech support

## Implementations
- Array or vector
  - Use array, front position, back position, current size
- Linked list

### Array implementation
- Enqueue
  - Increment `currentSize`
  - Increment `back`
  - set `theArray[back] = element`
- Dequeue
  - Set return value to `theArray[front]`
  - decrement `currentSize`
  - increment `front`
- `front()` and `back()` are the find operations
  - Tells you element at front and back, respectively
- Have to handle situation where array is full
  - Circular implementation: if front != 0, we can start filling empty spaces "before" the beginning of the queue in its backing array.

#### Abstract Data Types
- List, for example
  - Collection of "things" with a set of operations that can be performed on the structure
- A C++ class is a way to implement an ADT, as is Java, or C


#### Implementations of Lists
- Arrays
  - Fixed capacity
  - Operations
    - printList, find: linear time
    - findKth: constant
    - insert and delete: varies (can be constant, but worst case linear)
      - insert, if space: constant; if no space: linear (copy array to higher capacity duplicate)
- Linked list
  - Nodes contain an element, and a pointer to the successor node
  - Operations
    - printList, find, findKth: linear time
    - remove: constant
    - insert: constant

#### STL: String
- Standard template library's string
- Provides string class and member functions
- Operations
  - size() & length()
  - at() - with bounds checking
  - operator[] - without bounds checking
  - operator+= - concatenation
  - c_str() - returns c style string
  - substr()

##### Comparing Strings
- In java, we use .equals() to compare strings, since == compares for reference equality
- In C++, the operator override means == compares for equality
- But if we have pointers, == compares for reference equality


## Number representation

### Introduction

- Which is bigger?
  - 5, 8, or 12
  - Sorting numerically, or lexicographically (12 comes before 5 comes before 8)
- Which is "five"?
  - five or V or "cinq" or `101`
- We use numerals to represent numbers

### Positional number systems

- Integers
  - 346 = 3 * 10^2 + 4 * 10^1 + 6 * 10^0
  - 346 = 2^8 + 2^6 + 2^4 + 2^3 + 2^1
  - d_n d_n-1 … d_0 = sum(i-0,n)(d_i * R^i)
- Reals
  - d_n d_n-1 … d_0 . d_-1 d_-2 … d_-m = sum(i = -m, n) d_i * R^i

### Radix conversion

Radix R to decimal:
n = d_nR^n + … + d_0 * R^0

Decimal to radix R:
n/R = d_nR^(n-1) + … + d_1R^0, remainder d_0

examples:

42 base 5:
  = 2 * 1 + 4 * 5 = 22 base 10

121 base 3:
  1 * 1 + 2 * 3 + 1 * 9 = 16 base 10
