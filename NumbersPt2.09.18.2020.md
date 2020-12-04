# NumbersPt2
# Notes 09.18.2020 - notes

## Real number representation
- How do we represent real numbers at the CPU level?

### Fixed point representation
- Radix point is fixed at some position
  - Integer arithmetic with decimal point (2 points after the decimal)

### Floating point
- Each number in scientific notation has four parts:
- `-3.24 * 10^6`
  - Sign bit (1 is negative) - 1
  - Mantissa (the "value" of the number) - 3.24
    - Always in the range 1.0 <= `m` < 10.0 for decimal
    - Always in the range 1.0 <= `m` < 2.0 in binary
  - Base
    - 10 for typical scientific notation, but 2 for computers
  - Exponent
    - Power of base

### Conversion
`-3.24 * 10^6`
- Make mantissa between 1 and 2
- Make base 2
E.g., 106 = 1.06 * 10^2 = 1.65625 * 2^6

### IEEE 754 Floating Point Single Precision
Most common floating point spec
- 32 bits split as follows
  - bit 1: sign bit, 1 means negative
  - bits 2-9: exponent, 8 bits
  - bits 10-32: mantissa (23 bits)
- Exponent values:
  - 0: zeros
  - 1-243: exponent - 127
    - 127 is the *exponent offset* or the *bias*
    - So any raw value less than 127 corresponds to a negative exponent
  - 255: infinities, overflow, underflow, NaN
- Desiderata:
  - an all-zero floating point number is zero, just like how an all-zero integer is 0. Casting zeroes is easy, it's a no-op

- Mantissa
  - bits $b_1$ through $b_23$
  - The mantissa, in base 10, is equal to:
    - 1.0 + the sum from i = 1 to 23 of $b_i$ over $2^(-i)$
    - Mantissa encodes everything *after* the decimal, which is why we add 1.0
    - **Question:** If all bits are 1, what do we get?
      - 1 + (almost)1 = (almost 2) = 1.99999999 (recognize it?)
      - Minimum value is all zeroes = 1.0

#### Converting a float from binary to decimal
`0x41c80000` (big-endian) = 0100 0001 1100 10000 0000 0000 0000 0000
**Sign bit**: 0, positive
Exponent: 1000 0011 = 0x83 = 131
  Offset: 127
  Real exponent = 2^4 = 16
Mantissa: 100 1000 0000 0000 0000 0000 = 1.5625 

= 1.5625 * 16 = 25.0

#### Floating point maximum (finite) positive value
- Largest float has:
  - 0 sign bit
  - 254 as exponent (1111 1110)
    - 255 reserved for infinities and overflows
    - = 127 exponent
  - All 1s for the mantissa
    - Yields (almost) 2
  - 2 * 2^127 = 2 ^ 128 = 3.402823 * 10^38

#### Floating minimum (finite) positive value
- 0 sign bit
- binary 1 as exponent (0 is reserved for zeroes)
  - = -126
- all 0s for mantissa
  - 1.0 as mantissa
- = 2 * 2^-126 = 1.175494 * 10 ^ -38

#### Floating point and spacial (un)uniformity
- Consider a numerical type that only holds 3 decimal digits
- 1.23, 12.3, 123, 1230
  - In scientific notation: 1.23 * 10^0, 1.23 * 10^1, 1.23 * 10^2, 1.23 * 10^3
- Consider the 'next highest number' for each of them (closest successive value that our representation can, well, represent)
  - 1.23 => 1.24; difference is 0.01
  - 12.3 => 12.4; difference is 0.1
  - 123 => 124; difference is 1
  - 1230 => 1240; difference is 10
- Depending on the exponent, the difference between two successive numbers is not the same.

Takeaway: increasing your mantissa bit by the smallest possible value results in bigger "real" changes for larger exponents.

#### Convert from decimal to binary
- Sign: 1 if negative, 0 if nonnegative
- Convert the float to a sum of powers of 2
  - 42.125 = 32 + 8 + 2 + 1/8
  - = 2^5 + 2^3 + 2^1 + 2^-3
- Convert to binary
  - = 101010.001_2
- Move the decimal to put in scientific notation
  - 101010.001_b = 1.01010001_2 * 2^5
  - Slides over five spots, so the exponent is 5

#### Converting to powers of 2
- Consider 42.125
- For integer portion:
  - Repeatedly subtract the highest power of 2 that is less than the number
  - 42 - 32 => 10 - 8 => 2 - 2 => 0
  - 42 = 32 + 8 + 2 = 2^5 + 2^3 + 2^1
- For decimal portion:
  - Repeatedly subtract the lowest power of 1/2 that is less than the number
  - Consider .65625 = 21/32
  - 21 / 32 - 16/32  = 5 / 32 - 4 / 32 => 1/32 -1/32 = 0
  - 0.65625 = 1/2 + 1/8 + 1/32

#### Checking your work
- Use `union` to write a float into some space in memory
- Read out the float as an integer

