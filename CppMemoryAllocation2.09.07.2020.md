# C++ Memory Allocation - 2
# Notes 09.07.2020 - notes

### Dynamic example
Assume `int *x` is declared, and `int y` is from user input

```cpp
x = new int[10]; // 40 bytes
x = new int; // 4 bytes
x = new int[y]; // y*4 bytes
```

Question: how does C++ know how much memory to allocate?

Enter: `sizeof` operator
`sizeof(type)` returns the size of type in memory, in bytes

```cpp
#include <iostream>
using namespace std;

class Foo {
	long x, y;
};

int main() {
	cout << "sizeof long: " << sizeof(long) << endl;
	cout << "sizeof Foo: " << sizeof(Foo) << endl;

	Foo *qux = new Foo(), *bar = new Foo();

	cout << "1st foo: " << qux << endl;
	cout << "2nd foo: " << bar << endl;

	long diff = ((long)bar) - ((long)qux); // cast pointers to longs, do arithmetic
	// clean up after ourselves
	delete qux;
	delete bar;
	return 0;
}
```

Results:
```
sizeof(long) -> 8
sizeof(Foo) -> 16
difference: 32
```
Why the extra space? Why 32 bytes for a 16 byte value?
Answer: compiler needs to take some space for "record keeping;" how large is the structure?

- Keep track of how much space was allocated
- Therefore: dynamic allocation is not as space-efficient at storing data

### Why do pointers segfault?
- They're NULL
- They're uninitialized
- They're dangling (their pointee has been freed)

#### A safer alternative: references
- Reference: 64-bit memory address pointing somewhere
- Declaring a reference
```cpp
List sampleList;
List &listRef = sampleList;
```
- References, like pointers, hold an address; three primary differences:
  - Its address cannot change (kinda like a 'constant pointer')
  - It must be initialized upon declaration
  - Has implicit dereferencing
    - C++ has no special syntax, assumes you're talking about referent when you interact with a reference

#### swap() with pointers
```cpp
void swap(int *x, int *y) {
  int temp = *x;
  *x = *y;
  *y = temp;
}
```

#### swap() with references
```cpp
void swap(int & x, int & y) {
	int temp = x;
	x = y;
	y = temp;
}
```

#### Accessing members through a pointer
```cpp
class Square {
	public:
		Square(int sideLength);
		int sideLength;
}

int main() {
	Square *squarePtr = new Square(1);
	int length = squarePtr->sideLength;
  delete squarePtr;
	return 0;
}
```

Through a reference:
```cpp
class Square {
	public:
		Square(int sideLength);
		int sideLength;
}

int main() {
	Square square = Square(1);
  Square & squareRef = square;
  int length = squareRef.sideLength;
	return 0;
}
```

In a definition, `*` modifies a type making it a pointer.
In a statement, `*` dereferences a pointer.

In a definition, `&` modifies a type making it a reference.
In a statement, `&` returns an object address.

### Call by value
Actual parameter is copied into formal parameter
```cpp
int max(int a, int b);
void swap (int *x, int *y);
bool compare(Rational left, Rational right);
```
We can also pass *pointers* by value. 

Efficiency issues: call-by-value can cause the copying of large data structures that really don't need to be copied.

Solution: call by reference
```cpp
void swap(int &x, int &y);
```

User when formal parameter should be able to change the value of actual argument. This is the purpose of references.

**Question**: what if we want to pass a parameter by reference, but we want to be assured that the caller won't modify its value?
- Capture efficiency gains of pass-by-reference without allowing mutability

**Answer:** `const` references.

```cpp
bool compare(const Rational & left, const Rational & right);
```
Used when there is a class type that cannot be changed by formal parameter.
Compiler will enforce that you don't write to the reference.

### References in return values
- Return by value
  - Return a copy (potentially of a pointer)
- Return by reference
  - Return a reference
- Return by constant reference

### Default implementations
C++ provides by default:
- Default constructor
- Copy constructor
- Destructor
- operator=()

##### Default constructor
- Takes no parameters
- Doesn't do anything by default

##### Destructors
- Called whenever an object:
  - Goes out of scope, or
  - `delete` is called
- Frees up any resources allocated during the use of an object
- Cleans up:
  - Dynamically allocated objects using new
  - Can also free: files, network sockets, etc.
