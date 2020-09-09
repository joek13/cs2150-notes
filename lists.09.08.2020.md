# Lists
# Notes 09.08.2020 - notes

## Recap: things C++ provides by default for all classes
- Default constructor
- Copy constructor
- Destructor
- operator=()

### Default constructor
- Takes no parameters,
- Does nothing by default
-

### Default destructor
- Called when an object
  - Goes out of scope
  - `delete` is called

### Copy constructor
- Special constructor that creates a new object, initialized to a copy of the same type
- Called in the following situations:
  - Declaration with initialization
e.g., 
```cpp
IntCell original;
IntCell copy = original;
```
   - If object is passed by value into subroutine
   - If object is returned by value from subroutine

### operator=()
- Also known as the "copy assignment" operator
- Copy the state of the original into copy
- Called when = is applied to two objects AFTER both have already been constructed
```cpp
IntCell original;
IntCell copy;
copy = original;
```
- Example of an *operator override*

### Operator overrides
- You can override operators for a given class
- Overriding the `operator<<()` method allows you to control how `cout << i` works 
- Can override operations for your own numerical types, for example

# Lists (and a bit more C++)
## Standard library: vector
- "Standard template library"
- Vector is "basically an array"
  - Replacement for C++ builtin array
  - Array problems:
    - Can't be copied with =
    - No notion of capacity
    - No way to check index validity
- string
  - Replacement for C-style strings (char*)
  - C strings cannot compare correctly with ==

## Vector
- `#include <vector>`
- Operations
  - `Object & operator[](int idx)` -> no bounds checking
  - `Object & at (int idx)` -> does bounds checking
  - `int capacity() const`
    - How much space is there?
    - (How many elements can we have before doubling the backing buffer?)
  - `void reserve(int newCapacity)`
    - Reserves some spaces
  - `int size() const`
  - `void clear()`
  - `bool empty() const`
  - `void push_back(Object e)`
    - Adds an element to the first unoccupied spot
  - `void pop_back()`
    - Removes the highest-indexed element
  - `const Object & back() const`
    - Last element
  - `const Object & front() const`
    - First element

## Iterators
- iterator
  - Nested type that represents position
  - STL's vector provides
    - iterator begin()
    - iterator end()
  - iterator methods
    - `itr++`, `++iter` -> moves forward
    - `itr--`, `itr--` -> moves backward
    - `*itr` -> returns ref to obj at itr
      - Overrides the unary `*` operator
    - `itr1 == itr2` -> true if same location
    - `itr1 != itr2` -> true if different location
    -

## Iterator usage
```cpp
vector<int> v;
for(vector<int>::iterator it = v.begin); it != v.end(); it++) {
	cout << *it << endl;
}
// prints out every element in v
```
*Wait, what's that type?* `vector<int>::iterator` is just the nested iterator type for vectors over `int`s

## Iterators, continued
- Vector operations that require iterators
  - `iterator insert(iterator pos, const Object &x);`
  - `iterator erase(iterator pos);`
  - `iterator erase(iterator start, iterator end);`
