# C++ Preprocessor
# Notes 09.02.2020 - notes

## Preprocessor
First stage of compilation, handles any statements beginning with `#`.

`#include` - direct copy of file to location specified
In general, only #include `.h` files.

`#ifndef` - 'if not defined'
See also: `#ifdef`, `#if`

`#endif` - ends any `#if`

`#define` - defines a macro (direct text replacement)
```cpp
#define TRUE 0
if(TRUE == 0) { /* this branch will run */ }
#define MY_OBJECT_H
// doesn't have to define a value, can be just defined
// can test if it's defined later (useful for include guards)
```

### `#define` example
```cpp
#define FOO
#ifdef fOO
/* this is compiled */
#endif
#ifndef FOO
/* this isn't compiled */
#endif
```

### Why do we need the preprocessor?

`odd.h`:
```cpp
#include "even.h"
bool odd(int x);
```
`even.h`:
```cpp
#include "odd.h"
bool even(int x);
```

But wait! We get an *include loop*.
  `odd.h` -> `even.h` -> `odd.h` -> `even.h`
#### Preventing include loops
Solution:
```cpp
#ifndef ODD_H
#define ODD_H
#include "even.h"
bool odd(int x);
#endif
```
`even.h`:
```cpp
#ifndef EVEN_H
#define EVEN_H
#include "odd.h"
bool even(int x);
#endif
```

## Pointers
- Store a memory address of another object
- Can be a primitive type or a class type
- "Pointers are the same things as references"
  - Not true, but useful to think about it this way for now.

#### Syntax
- `int * x;` declares variable x, which is a pointer to an int
- `char *y;` declares variable y, which is a pointer to a char
- `Rational * rPointer;` declares variable rPointer, which is a pointer to a Rational

`int`, `char`, and `Rational` are all different sizes, but pointers to any object are always the same size (in this case, 8 bytes)

#### C++ Syntax: *
- Asterisk: *
  - In a definition
    - Defines a pointer type;
  - In an expression
    - "Dereference" operator
    - Evaluates to the object pointed to by the pointer
      - `*x = 2`, set the int value at x to 2

#### C++ Syntax: &
- Ampersand: &
  - In a definition
    - Defines a reference type 
  - In an expression
    - "Address of"
      - `x = &y;` where `x` is `int*` and `y` is `int`
      - Sets x to the address of y. x now points to y.

#### What types are these?
- `char * x` -> pointer to a char
- `int **y` -> pointer to a pointer to an int
- `Square **blah` -> pointer to a pointer to a Square

### Pointer pitfalls
- Runtime (not compiler) errors
```cpp
int n = 30; 
int * p; // never assigned an address
*p = n;
```

```cpp
int *p = NULL; // better code
```

### Note on initialization
C++ does not automatically initialize anything.
So:
```cpp
int x; // x is filled with garbage memory, not initialized
```

### Invalid memory addresses
- If you try to access memory that hasn't been given to you, you get a segfault.

#### swap example
```cpp
void swap(int *x, int *y) {
	int temp = *x;
	*x = *y;
	*y = temp;
}
```
