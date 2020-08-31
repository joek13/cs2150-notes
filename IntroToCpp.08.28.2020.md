# Introduction to C++
# Notes 08.28.2020 - notes

## Introducing C++
### Why C++?
- Good to learn a second language
- Efficiency
	- Can be more efficient
	- More control of underlying memory management, etc.
- C++ lets us get "under the hood"
	- Program and data representation (!) in memory
	- Memory allocation

### Brief C History
- Created in 1972 by Dennis Ritchie
- C++ created in 1985 by Bjarne Stroustrup
	- Added classes and maintained backwards compatibility
	- Had some pretty terrible syntax.

### "Hello World" in Java and C++
**Java:**
```
public class HelloWorld {
	public static void(String [] args) {
		System.out.println("Hello World!");
	}
}
```
**C++:**
```
#include <iostream>
using namespace std;
int main() {
	cout << "Hello World" << endl;
	return 0;
}
```

Similarities:
- Procedural, line-by-line execution
- Return types, semicolons to terminate statements, strings literals using ""

Differences:
- #include vs import
	- Preprocessor directive in C++
- Namespaces
	- C++ parallel to Java packages
- main method
	- C++ doesn't get argv as String[] argument
	- C++ returns a status code (0 for normal, non-error termination)
		- **NOTE:** submissions for PDR need to return 0, the grader considers nonzero status codes a runtime error
	- output streams, stream concatenation operator, endl constant for ending a line

### The main function
- Not part of any class--it's a function
- Must be global
- Must have return type of int
	- Must return 0 for non-erroneous termination

### Preprocessor 
- Acts before compilation
- e.g.,
	- `#include <iostream> // includes system file`
		- angle brackets represent system files, resolved by compiler and linked libraries
	- `#include "ListNode.h" // include user file`
		- quotes represent local file 
	- compiler provided imports have no suffix, aren't headers
	- user-provided imports are .h (header) files

### Compilation process
1. Preprocess source file
2. Compile resulting file
3. Link the resulting files from step 2 (more later)

### Using directive
- Similar to Java's import
	- Uses a namsepace
	- "Scopes us in" to the namespace in qeustion, so we no lnoge rhave to type it

### I/O
- Basic I/O
```
// iostream library
#include <iostream>
using namespace std;
int main() {
	int x;
	cout << "Enter a value for x: ";
	cin >> x;
	return 0;
}
```

### C++ Primitives
- int
	- Can be 16, 32, 64 bits depending on platform
	- all of the architectures we use will be >=32 bit
- float
	- ~7 digits of accuracy
- double
	- ~14 digits of accuracy
- char
	- c++ generally uses 8bit ascii encoding-
- boolean

### C++ Operators & Expressions
- if statement
	- Condition can be int or bool
	- Catch: assume x is an int
		- if( x )
			- Valid
			- Goes ahead if x is nonzero
			- Goes to else if x is zero
		- if ( x = 0 )
			- Will *not* emit a compiler error
- Loops, same as java

### Compiler choice
- In the past, we've used g++
	- GNU compiler collection
- We now use clang++ from LLVM
	- Much better error messages (spelling corrections a la "did you mean \_\_?")

### Functions
- Methods that aren't a member of a class

### Mutually recursive functions
```
bool odd(int x) {
	if (x == 0) {
		return false;
	}
	else
		return even(x-1);
}

bool even(int x) {
	if (x == 0) {
		return true;
	}
	else
		return odd (x-1);
}
```
The compiler is not happy with this.

Why? Because even is not defined when we define odd. Or we switch the order, and odd isn't defined when we define even.

How do we solve this? Define a *function prototype* using a *forward declaration*
```
bool even(int x); // forward declaration
bool odd(int x) {
	if (x == 0) {
		return false;
	}
	else
		return even(x-1);
}

bool even(int x) {
	if (x == 0) {
		return true;
	}
	else
		return odd (x-1);
}
```
