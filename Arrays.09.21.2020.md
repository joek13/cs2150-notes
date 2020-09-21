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
  - Compares pointers

...but `vector` has all of these.

```cpp
int someInts[3];
/*
  Here, the size is only used to set aside memory for the array.
  It's not stored anywhere.
  someInts is set to &someInts[0], the array's "base address."
*/

int* someInts = new int[3];
/*
  Declared dynamically.
  Need to delete the array later.
*/
```
