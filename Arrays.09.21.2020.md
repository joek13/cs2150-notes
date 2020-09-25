# Arrays
# Notes 09.21.2020 - notes

## Converting a float from decimal to binary

### Input: 42.125
- Sign bit: 0
- Convert to a sum of powers of 2
  - 42.125 = 2^5 + 2^3 + 2^1 + 2^(-3)
- Convert to binary
  - 101010.001
- Scientific notation: 1.01010001 * 2^5

#### Recall: converting to powers of 2
- For the integer portion:
  - Repeatedly subtract the highest power of 2 that is less than or equal to that number
  - 42 - 32 => 10 - 8 => 2 - 2 => 0
  - 2^8 + 2^3 + 2^1
- For decimal portion:
  - Repeatedly subtract the highest power of 2 (lowest power of 1/2) that is greater than number

#### Convert to binary
- Sign bit: 0
- Exponent: 8 bits
  - Bias is 127, so 5+127 = 132 = 1000 0100
- Mantissa:
  - Basically just the portion after the decimal
  - 010 1000 1000 0000 0000 0000

So:
`01000010001010001000000000000000`

#### Floating point error
- 1/10 = 0.1 in decimal
- Has repeating decimal in binary representation
- Causes issues when we compare floating point numbers that should be equivalent
 **Solution**
 Define a constant `EPSILON` and check that `fabs(a-b) < EPSILON` where `fabs` is the floating point absolute value function from `math.h`

- Rounding error occurs in our floating point for numbers whose denominators are not multiples of 2

#### Solutions
- Use rational numbers when possible
- Use more digits
  - Doesn't eliminate error, but does make them smaller

#### Better floats, more bits
- IEEE 754 double precision float (64bit)
  - 1 sign bit
  - 11 exponent bits
  - 52 mantissa bits
  - Exponent bias is 2^(e-1) - 1 = 1023

## Arrays and Big-O
### Primitive Arrays
Two ways to declare (both static):
```cpp
int someInts[3]; // name, type, size
int someInts[] = { 4, 37, 18 }; // name, type, initialization list
```
In memory, an array consists of:
- A pointer to the beginning of the array
- A block in memory representing the array

*Note*: array base names (`someInts` in this example) act like constant pointers, and cannot be reassigned. The compiler needs to know where the base name points so that it can deallocate the array when we're finished with it.

#### Things to remember
- No index checking
  - Out-of-bounds access is not checked at compile time or runtime. Out of bounds access causes unpredictable behavior
- When passed as an argument to a function, we need to tell the function the array's size 
  - Function has no way to know the array's size
- Cannot be copied with =
  - Needs to be copied element-by-element
- Cannot be compared with ==
  - Won't do what you want it to do.
  #- Compares pointers

...but `vector` has all of these.

```cpp
int someInts[3];
/*
  Here, the size is only used to set aside memory for the array.
  It's not stored anywhere.
  someInts is set to &someInts[0], the array's "base address."
  Acts kind of like a "constant pointer." You can't reassign the pointer because that would prevent the compiler from deallocating the memory when the subroutine returns.
  Size has to be known at compiletime.
*/

int* someInts = new int[3];
/*
  Declared dynamically.
  Need to delete the array later, the onus is on the programmer.
*/
```

#### In memory
How do we calculate the location in memory for a given index?

```
&someInt[i] = {addr of someInts} + (sizeof(int) * i)
```
- Base address plus struct size times index
  - If you try to access something larger than the array itself, you're reading some memory address *way* out there. Maybe you get a segfault (if you're lucky)

Want bound checking? use a `vector`

#### Operations on arrays
`someInts = someOtherArray;` illegal
  - Array base addresses are considered constant
  - One instance of pointers and arrays not being equal
`someInts == someOtherArray` is legal, but...
  - This just checks if they point to the same location.


#### Function calls and arrays
```cpp
void someFunc(int arrayOfInts[]) {
	// how many elements in arrayOfInts???
  // need to pass a length parameter.
}
```

#### Multidimensional arrays
- Stored in *row-major* order, like how you read a book.
  - Lays row 0
    - Element 0, 1, 2, …
  - Lays row 1
    - Element 0,1,2, …
- How do we access `arr[i][j]`
  - Skip over `i * size of row`
  - Skip over `sizeof(element) * j` to get to the cell in that row
- Consider a `2x3` multidimensional array.
  - `arr[0][4321]` -> likely segfault
  - What about: `arr[0][5]`?
    - There's no 5th element in the 0th row, but...
    - Row-major order means we actually access `arr[1][2]`

#### Command-line parameters
- `main()` can be defined as either:
  - `int main()`
  - `int main (int argc, char **argv)`
    - Or: `int main(int argc, char* argv[])`
    - `argc` = "arg count" = number of arguments
    - `char **argv` / `char* argv[]` = array of c-strings representing the arguments
- Note that `argv[0]` is always `./a.out` or whatever your executable
  - So `argc` is 1 in minimal case

#### Dealing with c-strings
Just use the `string(char** cstring)` constructor in c++'s string library

## Orders of Growth
Test program running time at different input sizes
Want to classify this relationship into an *order* of function
O(n) defines upper bound on worst case runtime
Omega(n) defines lower bound on best case runtime

We're looking for a "tight bound"
O(n) is an upper-bound OR a tight bound
Omega(n) is a lower-bound OR a tight bound
Theta(n) is a tight bound

### Classifying functions by their asymptotic complexity

- Asymptotic growth rate or asymptotic order
  - Ignore constant factors and small inputs
- Omega, theta, and O are functions that define sets of functions
  - Omega(g) : functions that grow at least as fast as g
  - Theta(g) : functions that grow at the same rate as g
  - O(g): functions that grow no faster than g

### Why do we care?
- Some data structures are faster than others
- How do we compare them?

### Algorithm doesn't matter if you have 10 elements
- A bogosort works on 10 elements.
- But for big input sizes?
  - UVa has 100,000 email addresses

### Assumptions
- Whatever algorithm we're measuring--we have an accurate representation of how long it takes
- We have some other benchmark function g we're comparing to
- We're comparing f(n) to g(n) s.t.,
  - f(n) in O(g(n))
    - f of in is in the order of functions with worst-case runtime g(n)

### Worst-case scenario
- We always analyze the worst case runtime
- It makes no sense to analyze the best case
  - The average case may not be representative

### Formal defn.
- f and g functions from nonnegative integers into the positive real numbers
- For some real constant c > 0 and nonnegative integer constant n_0,
- O(g) is the set of functions f s.t.
  - f(n) <= c * g(n) for all n >= n_0
- Omega(g) is the set of functions f s.t.
  - f(n) >= c * g(n) for all n >= n_0
- Theta(g) = O(g) intersect Omega(g)
