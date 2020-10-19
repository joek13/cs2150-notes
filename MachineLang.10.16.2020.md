# MachineLang
# Notes 10.16.2020 - notes

## IBCM ( Machine Language )
- IBCM is a toy machine language created for 2150
- Machine language
  - Instructions represented as patterns of bits that can be understood
  - Stored in main memory
- Assembly language
  - Human-readable notation for machine language used to control a specific computer
  - Translated by an assembler into the bits

### Why learn about assembly?
- Machine designers
- Compiler writers
- Programmers (especially for OSes)
- Assembly language programmers
  - Some hyper-super-ultra-performance-critical tasks are still handwritten in assembly
- Most importantly: helps you understand how computation happens

### High-end memory hierarchy
- CPU registers
  - 1 access per CPU cycle
  - 3 * 10^9 accesses per second
  - 1 Kb total storage
- Cache - middle-ground; much faster than memory, and much more storage than CPU registers
  - ~ 10 cycles
  - Speeds up successive accesses
  - SDRAM: 10ns
  - 10^8 accesses per second
  - Multiple levels possible
  - Higher levels are bigger and slower
  - 1 Mb total storage
  - Cache levels
    - Lower the number, the faster (and smaller) it is
    - L1 - fastest, smallest-capacity cache
    - L2 - slower, larger-capacity
    - L3 - …?
- Memory
  - ~100s of cycles
- Disk
  - (Sometimes used as swap space for memory)
  - ~millions of cycles
  - (depends on disk size)


- Page - 1Kb segment of memory

## Fetch-execute cycle
```
while(power is on) {
  IR = memory[pc]
  pc += len(instruction)
  execute instruction in IR
}
```

## Instructions
x86:
```
add eax, ebx
add ecx, 1
```
Explicit use of registers

IBCM:
```
load 100
add 200
store 300
```
Implied use of accumulator
IBCM only has one register
Load takes value at 100 in memory, loads into accumulator
Add loads value at 200, and adds it
Store takes value in accumulator and writes to memory at 300

## IBCM Description: the CPU
Single accumulator
- 16 bits
Special purpose register
- IR: instruction register
- PC: program counter

4096: 16-bit words
- Word: "chunk size" or addressable unit
- All initialized to zero initially
  - (Unlike c/c++)

```
+++++++++++++++++++++++++++++++++++++++++++++++
+ 15 14 13 12 + 11 10  . . .               0  +
+++++++++++++++++++++++++++++++++++++++++++++++
+  0  0  0  0 + (unused)                      + halt
+  0  0  0  1 + I/O op   | (unused)           + I/o
+  0  0  1  0 + shift op | (unused) | (count) + shifts
+   opcode    + address                       + others
+++++++++++++++++++++++++++++++++++++++++++++++
```

### Instructions
- 4-bit opcode = 16 instructions

**Halt:** `0000`
Halts the IBCM, ignores input

**I/O:**: `0001`
Next two bits are I/O type:
- Bit 11 specifies input (0) or output (1)
- Bit 10 specifies hex word (0) or ascii (1)
Reads or writes from accumulator

**Shifts:** `0010`
Next two bits specify shift type:
- Bit 11: shift (0) or rotate (1)
- Bit 10 specifies direction: left (0) or right (1)

E.g., consider bits `0000 1111 0000 1111` shifted by 3 bits
- 00 - shift left - `0111 1000 0111 1000`
- 01 - shift right - `0000 0001 1110 0001`
- 10 - rotate left - `0111 1000 0111 1000`
- 11 - rotate right - `1110 0001 1110 0001`

When we shift "outside" of the number, we just fill in with zeroes.
When we rotate the number, we fill in with the other side; bits "wrap around"


**Remainder of the instructions:**
- first 4 bits: instruction
- remaining 12 bits: address; interpreted differently by different instructions

A few instructions: 
- 1111 = BRL = "branch and link"
  - Jump to some address, put old address in accumulator
  - Allows us to define subroutines


**IBCM:**
- 16-bit instructions ("word size")
  - 4-bit opcode
  - 12-bit operands

### Execution example
Step-by-step
*Note:* what is program, what is data???

### Writing IBCM code
6-step plan
1. Write high-level pseudo-code
2. Translate into IBCM assembly instructions
3. Test code by hand
  - Pen-and-paper debugging
4. Encode into machine code
5. Load and run

### Computing a sum in IBCM

Pseudocode:
```cpp
read n;
i = 1;
s = 0;
while(i <= n) {
  s += i;
  i += 1;
}
print s;
```

```
mem    locn   label   op    addr    comments
C00A   000            jmp   start   
0000 001 i dw
```


e.g.,

```cpp
if(B == 0)
  S1;
else
  S2;
```

```
// assume b is initialized somehow
load b
jmpn S1
jmp S2

S1:
; code

S2:
;code 
```


### Summing up the elements of an array
- Take in: the address of the array's first element
- The # of elements in the array

**Problem:** IBCM doesn't have array subscripting (indexing)

How do we do this?
**Declare variables:**
- i, index counter
- s, ongoing sum
- a, start pointer
- n, size of array
- zero, zero constrant
- one, one constant
- adit, 5000

Instructions:
```
jmp start

dw i 0
dw s 0
dw a 0
dw n 0
dw zero 0
dw one 1
dw adit 5000;

start:
  readH ; read array address in hex
  store a
  readH ; read array size in hex
  store n
  load zero ; i = 0; s = 0;
  store i
  store s

loop: load n ; if i >= N goto xit
  sub i
  jmpl xit
  jmpe xit
  load adit ; 5000 = add instr with 000 as "target"
  add a ; add our array base
  add i ; add our array index
  store doit ; store into the doit instruction
  load s ; put running sum into accumulator
doit: nop ; will be overwritten -- computes sum
  store s ; store into the sum
  load i ; increment i
  add one
  store i ; store incremented value
  jmp loop ; goto loop
xit:
  print s ; print sum
  halt
```

## `adit` is kinda crazy
- Builds an instruction out of data
- Kind of like dynamic code generation
  - We build an add instruction
  - `5000` = `adit` (the add instruction)
  - `5003` = `add a` (add array base)
  - `5001` = `add i` (add array index)
  - Now, the accumulator is an instruction that adds the value at `arr[i]` in memory to the accumulator
- Self-modifying program
  - In practice: usually disallowed by modern OSes
  - ACE exploits

### IBCM Recap
- Memory
  - Array of "words" / chunks of data
  - Data can be program
  - Program can be data
- IBCM is Turing-complete
  - Any program expressible in any programming language can be expressed in IBCM
    - (With standard caveat about limited memory)

### Tips/reminders
- Use steps
1. Pseudocode
2. Assembly code
3. Trace the assembly
4. Translate to machine code
5. Run in the simulator to verify that it works

### What's missing from IBCM?
- More registers
- Comments ?
- Multiplication / division
- Floating point
- Bigger address space
- What else??

## Splay trees
Self-balancing binary-search tree

**New operation:** ***splay***
Splay x:
1. Find x
2. Perform rotations until x is root
3. Return root
Not balanced in our familiar sense

What if we splay something that doesn't exist?
- When find returns nothing, we splay whatever the most recent node we saw was traversing
- Return root anyway


### What's the point?
Doesn't balance like we're used to
...but it keeps recently used values near the top of the tree
- Makes splay trees great for caches
- ...but not good if your accesses are uniform

(Easier implementation than red-black or AVL trees)

Insert x:
Splay x
Add x at appropriate location

Remove x:
Splay x
Remove x

Main operation: splaying

## Tree operation time complexity
How long does a splay take?

Theta(h), where h is the height of the tree
...not good (linear time = linked list??)

But a splay tree is Theta(logn) amortized time

## Vectors 
- Consider insert:
  - worst case is that you have to double the size of the array (from n to 2n), and then copy the elements
  - Linear time!
- This doesn't happen that often... what about *amortized* time? 

Consider a vector with 100 reserved spaces
Inserting n elements requires about n-100 copies
The # of copies is proportional to n
Theta(n) worst case,
but Theta(1) amortized when executing many operations

### Applications
Trees are fast -- so when would we not want to use them?
- When there's no natural ordering
- When we want less complexity
- when we want Theta(1) on retrieves
- When we want Theta(1) for all operations

### Program tree
- Tree that represents the statements and their scope
- Parse tree / abstract syntax tree before compilation


- We can compare program trees, which are resitant to
  - Changed variable names
  - Comments
  - Function order

- "A system for detecting software plagiarism"
- Load up all programs...
  - and do n * (n - 1) = n^2 comparisons

- Displays most similar programs
