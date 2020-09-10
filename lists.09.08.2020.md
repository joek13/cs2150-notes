# Lists
# Notes 09.08.2020 - notes

[Lecture video](https://uva.hosted.panopto.com/Panopto/Pages/Viewer.aspx?id=31399556-0dcf-4d66-ba6c-ac2400dbae63)

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

## C++ Templates
Comes from the "standard template library"
- Java
  - Uses inheritance to write type-independent code
  - Or generics
- C++
  - Uses templates (precursor to generics)
    - Function templates
    - Class templates

```cpp
template <typename Comparable>
const Comparable & findMax(const vector<Comparable> & a) {
	int maxIndex = 0;
	for(int i = 1; i < a.size(); i++()) {
		if(a[maxIndex] < a[i]) // note use of < operator
			maxIndex = i; 
	}
	return a[maxIndex];
}
```
Template function: can find the max value of any type!

**Static dispatch:** The compiler creates, transparently, different copies of your template class for every type you plug into that template.
  - Compiler automatically generates a vector for int, a vector for string, etc., etc.
  - Compiler automatically generates a `findMax` for `int`, for `double`, for `string`
- What happens if the compiler tries to generate a `findMax` for `IntCell`?
  - `IntCell` has no operator override for `>`, uh oh!
  - Compiler generates code that doesn't compile
    - Where to report this error?
    - How do we tell the user that code they didn't write is wrong?
    - So, we get.... shitty error messages. 
  - So we just don't write templates in this course.

#### Note on default initialization for templates
```cpp
template <typename Object>
class ObjectCell {
	public:
		ObjectCell(const Object & initValue = Object()) // Object() uses the default initializer for whatever type Object is. 0 for int, false for bool, etc.
			: storedValue(initValue) {}
		const Object & getValue() const {
			return storedValue;
		}
		void setValue(const Object & val) {
			storedValue = val;
		}
	private:
		Object storedValue;
};
```
### Logistical note on STL usage
- "You may use any STL class with the following restriction:"
  - Unless otherwise specified, do not use the STL data structure which is the purpose of the lab
    - Don't use `hash_map` on the hash lab
- Useful throughout the course:
  - `vector`, `string`
  - `stack` in prelab and inlab 3 (NOT postlab 3)

## Stacks
- Insert, delete, find can only be performed at the end ('top') of the stack
- LIFO: Last In, First Out
- Attributes
  - top of stack
- Fundamental operations:
  - Push: insert at top
  - Pop: delete at top
    - Exception on empty stack
  - Top: examine top of stack
    - Exception on empty stack

## Stacks
- Undo operation in programs
- Operator precedence
- Symbol balancing (parenthesis matching)
- Postfix calculators
- Activation records: list of active procedures and associated information
  - Call stack

## Symbol balancing
- Read characters to end of file
  - If opening symbol, push onto stack
  - If closing symbol
    - If stack empty, then invalid code
    - Else pop stack
    - If popped symbol is not corresponding opening symbol, then error
  - If we reach EOF with a nonempty stack, we have unbalanced symbols

## Postfix calculator
- For each token:
  - If number, push to stack
  - If operator:
    - Pop last two numbers from stack, apply operator, push result

## Call stack
- Enter new subroutine:
  - Create new activation record, push to stack
- Exit subroutine:
  - Pop activation record from the stack
  - Set context to new head record

## Stack implementations
- Singly linked list
- Push: insert at head of list
- Pop: remove from head of list
  - head = head->next
    - (remember to deallocate old head)
- Peek/top: inspect head element

## Array implementation
- Avoids links, *most* operations happen in constant time
- Stack consists of
  - Array
  - top of stack -> stack pointer
- Push
  - Increment stack pointer
  - Set `stack[sp]` = new value
- Pop
  - Fetch value at `stack[sp]`
  - Decrement stack pointer
- Top
  - Fetch value at `stack[sp]`
- What if the array is full?
  - Uh oh... stack overflow 

## Vector implementation
- Abstract datatype is exactly the same, even though the implementation differs
