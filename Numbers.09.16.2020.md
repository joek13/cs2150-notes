# Numbers
# Notes 09.16.2020 - notes


## Base representation
When we run out of digits, we use A-F

e.g., 0xFF (base 16, hexadecimal) = 15 * 16 + 16 = 255

## Recall: radix conversion

Radix R to decimal:
- Each successive digit in a place-value system is multiplied by one higher power of R

101 = 2^2 * 1 + 2 * 0 + 1 * 1 = 5

Decimal to radix R:
*"Repeatedly divide the number by the base, paying attention to the quotient and remainder."*

42_10 to base 5.
  42 / 5 = 8 remainder 2
  8 / 5 = 1 remainder 3
  1 / 5 = 0 remainder 1

Your answer: the remainders in reverse order
42 base 10 = 132 base 5

121 base 10 to base 11
  121 / 11 = 11 remainder 0
  11 / 11 = 1 remainder 0
  1 / 11 = 0 remainder 1

100 base 11 = 121 base 10

## What is the value of "101"?
In binary: 5
In octal: 65
In decimal: 101
In hex: 257

## Specifying bases
In C/C++, integers beginning with `0` are interpreted as octal
  - `073` = 73 base 8
Integers beginning with `0x` are interpreted as hexadecimal
  - `0xFA` = FA base 16
There is no (convenient) way to specify binary literals, so these are usually done in hex.

## Converting between binary and hex
Split the binary number into 4 bit chunks ('nibbles')
Convert each nibble into a single hex digit

Hex to binary is just converting each hex digit to 4 bits

Remember: it takes two hex digits to make a byte

## What is the int?

```cpp
int n = 0;
```

in C/C++, this is defined by the implementation
`unsigned int`:
- Can hold between 0 and UINT_MAX
- UINT_MAX must be at least 65535
- sizeof(int) will evaluate to the byte size of an int in C/C++

## Endian-ness
-  Where in memory/processor is the most significant digit?
-  Most computers are little-endian
  - Least significant digits first

#### Big-endian
- Most significant digit first
  - 1000 0000 0000 0000 = 2^15 = 32768

#### Little-endian
- Least significant digit first
  - 1000 0000 0000 0000 = 2^0 = 1

- Which is better?
- Note that although the bits are reversed, usually it is displayed with just the bytes reversed

## More on endian-ness
- Often refers to *byte* ordering—not *bit* ordering
- Consider `0xdeadbeef`
  - On a big-endian machine, that's `0xdeadbeef`
  - On little-endian machine, that's `0xefbeadde`

## Endianness
- A "religious" argument

Most of the time, when we talk about Endianness, we're talking about ***byte*** order, not ***bit order*** 

- Big-endian
  - The quick brown fox jumps over the lazy dog
- Little-endian
  - dog lazy the over jumps fox brown quick The

## Integer representation
- How do we represent negative values?
- Designate one bit, by convention leftmost bit, to be the sign bit
  - sign bit 1 for negative
  - sign bit 0 for positive

### Sign-and-magnitude representation
- Use the first bit as a sign bit, use the remaining n-1 bits to encode the number's absolute value
- Problem! This has two representations for zero
  - Doubles the amount of time it takes to compare some value to zero, since we have two distinct representations of zero

### One's complement representation
- Encode absolute value of the number using `n-1` bits
- If negative, flip all the bits
- Problem!
  - Still two representations for 0

### Two's complement representation
- Avoids two representations for zero
- A negative number has its bits flipped, then you add one
  - Shifts all the number by one, avoiding two representations for zero

n bits:
- max value: 2^(n-1) - 1
- min value: -2^(n-1)

7 base 10 = 0111
-7 base 10 = 1001

Advantages:
- Only one representation for zero, and all its bits are zero
- Algorithm for *n*-bit memory space
  - Positive number, encode normally in n-1 bits
  - Sign bit is zero, max value is 2^(n-1)-1
  - Zero is a "positive" number
- Negative numbers:
  - Take the absolute value, subtract that from 2^n, encode that value
  - Alternatively, encode absolute value, flip bits, add 1
- Addition "just works" like it does for positive numbers

#### Integer overflow
Two's complement representation means that adding values above `INT_MAX` causes it to "wrap around"

Add 1 to 127: `0111 1111` + `0000 0001` = `1000 0000` = -128

#### Unsigned types
- 32 bit signed `int` has range  -2^31 to 2^31 - 1
- Unsigned int: range 0 to 2^32 - 1
