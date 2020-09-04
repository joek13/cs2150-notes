# Dynamic Memory Allocation
# Notes 09.04.2020 - notes

[Lecture video](https://uva.hosted.panopto.com/Panopto/Pages/Viewer.aspx?id=171f97f0-0bda-4100-86bb-ac2400db6713)

### Recall
Pointers are just memory addresses
Pointers are not initialized to any specific location (usually, just somewhere "off into space"--outside of your program's memory)
& operator -> "address of" operator

## Static memory allocation
Compiler knows exactly how much memory to allocate

```cpp
// no new keyword necessary
// static memory allocation -> amount of space needed already known
int x; // sizeof(int)
int someArray[10]; // 10 * sizeof(int)
int address = someArray[3];
```

## Dynamic memory allocation
Sometimes, we don't know how much memory is needed in advance
Need to create space at runtime
`new` -> returns pointer to the newly created thing

`new something;` -> allocates space, calls constructor

```cpp
int main() {
	int n;

	// read in a value from user
	cout << "Enter an int:";
	cin >> n;

	// create an array of ints using n
	int * ages = new int[n];

	for(int i = 0; i < n; i++) {
		cout << "Enter a value:";
		cin >> ages[i];
	}

	for(int i = 0; i<n; i++) {
		cout << i << ": " << ages[i]
	}

	delete [] ages; // clean up memory used by calling delete
	return 0;
}
```

## new syntax
```cpp
int *intPointer = new int;
Rational *rPointer= new Rational;
int *intPointer2 = new int(0);
// allocate space on stack for integer
// allocate space for pointer
// make pointer point to new int
// initialize int to 0
Rational *rp2 = new Rational(1,2);
```
**Note**: the `new` keyword only allocates/initializes the object. Assignment to a pointer is what allocates/initializes the pointer.

New allocates, initializes an object and returns its address.

## Memory management
- Java is a garbage-collected language
  - Allocated memory is automatically reclaimed
  - Convenient but inefficient
- C++ does not have garbage collection
  - Programmer has to explicitly reclaim memory, or it can't be used
  - If you don't reclaim memory, you have a *memory leak*
    - Program slowly eats more memory, never frees unused memory

## Delete keyword
```cpp
delete rPointer;
delete intPointer;

delete [] ages; // how does this work if we don't know the length?
```
**IMPORTANT:** `delete` doesn't modify the pointer provided to it. It simply follows the pointer and deallocates the memory it's pointing to.

Reassign the pointer after you delete it.

`delete` changes the pointee, not the pointer.

Don't deallocate memory that's already been deallocated. -> *double free*

## Accessing parts of an object
For a regular (non-pointer) object, use a period:
```cpp
Rational r;
r.num = 4;
```
For a pointer, dereference it first:
  (can't access fields of a pointer, because pointers don't have fields)
```cpp
Rational *r = new Rational();
(*r).num = 4;
```
Shorthand:
```cpp
Rational *r = new Rational;
r->num = 4;
```

### Friend
- Sometimes other classes need to access private data members of another class
- Can allow this by declaring other classes as friends
e.g.,
```cpp
class ListNode {
	public:
		ListNode();

	private:
		int value;
		ListNode *next, *previous;

		friend class List;
		friend class ListItr;
};
```
`List` and `ListItr` can access `value`, `next`, `previous`

## Associativity of *
- Consider:
  - `char* x,y;`
- Type of `x` is `char*`
- But `y` is just `char`
- `*` is right-associative, the spacing should be:
  - `char *x, y;`
  - Although either spacing is syntactically valid...
- Put `*` with the name to avoid ambiguity
