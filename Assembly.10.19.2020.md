# Assembly
# Notes 10.19.2020 - notes

# 64-bit x86 Assembly
- We write in assembly
- An assembler converts to machine code

### A brief history
Intel makes several chips with different word sizes
Eventually: creates the 8086 chip (16-bit words)
- Intel iterates on 8086, making 80286, 80386 (32-bit words), 80486
- Each successive "*86" chip needs backwards compatibility 

Intel renames 86 line to "Pentium"
  (USPO says you can't trademark a number)

Every chip Intel produces (even today!) are backwards compatible, all the way to 1978

"x86" = "something" in the 86 line

The parts of x86 instruction set we use go back 30-40 years

### AMD vs. Intel
- AMD developed standard in 2000, releases chip in 2003
- Intel creates "Itanium" line, and it goes terrible
  - No compiler support, etc.

## x86 Overview
**Registers:**
- 16 registers
- Several "modes" for each register (for backwards compatibility)
  - Using register `a` as an example
  - `al` = lowest 8 bits
  - `ah` = second-lowest 8 bits
  - `ax` = 16-bit value (lowest 16 bits)
  - `eax` = 32-bit value (lowest 32 bits)
  - `rax` = 64-bit value (lowest 64 bits)

**Special registers:**
- `bp` = "base pointer"
- `sp` = "stack pointer"

- 128 bytes of registers

## Fetch-execute cycle
Same as IBCM
```
while(power is on) {
  IR := mem[PC]
  PC := PC + 1 (word)
  execute instr in IR
}
```

## Declaring variables in x86
`byte` - 1 byte (DB) declare byte
`word` - 2 bytes (DW)
`double` - 4 bytes (DD)
  - NB: a floating-point double is 8 bytes (64-bit floating point)
  - this is a totally different thing
  - named because it is a "double word"
`quadword` - 8 bytes (DQ)

`TIMES x DB 0` directive means "create x bytes of value zero"


```
section .data
a     DB           23
b     DW           ?
c     DD           3000
d     DQ           -800
x     DD           1,2,3
y     TIMES 8 D8   0
str   DB           'hello', 0 ; null-terminated string
z     TIMES 50 DD  ?
```

## MOV command
`mov <dest>, <src>`
Where `dest` and `src` can be:
- a register
- a constant
- a variable name
- a pointer: [rbx]
- \*You will often see movq or movl in disassemblies
  - Just changes how much data you move, depends on your specific assembly syntax

### Addressing memory
- Up to 2 registers and one 64-bit signed constant can be added together to compute a memory address
- One register can be pre-multiplied by 2, 4, or 8
  - word-align
  - double-align
  - quadword-align

```
mov rax, rbx ; copies 64 bits in rbx into 64 bits of rax
mov rax, [rbx] ; copies the value from the address stored in rbx : [] is pointer dereference
mov [var], rbx ; copies 64 bits from rbx into wherever varis stored
mov rax, [r13-4] ; copies from 4 bytes before the address stored in r13
mov [rsi + rax], cl ; cl = lowest 8 bits of rcx register, copies cl into location whose address is the sum of rsi and rax
mov rdx, [rsi + 4*rbx] ; uses "index" from rbx to compute location in array, copies value from that location into rsa
                       ; array dereferencing in one command!
```

**These don't work:**
```
mov rax, [r11-rcx] ; can't subtract 
mov [rax + r5 + rdi], rbx ; only up to two registers
mov [4*rax + 2 * rbx], rcx
```

### Example
```
; rax = 100, rbx = 108
mov rcx, rax
mov rdx, [rbx]
mov rsi, [rds+rax+16]
mov [rsi], 45
mov [a], 15
lea rdi, [a]
```

### `lea` - "load effective address"
"Get me the address of x"
`lea <dest>, [var]`
- Stores the address of variable `var` into `dest`

### Memory addressing restrictions
- Destination cannot be a constant
- Cannot access memory twice in one instruction
  - We don't have time at the high clock speed
  - So, the following is invalid:
    - `mov [rax], [var]`
    - `mov [rax+8], [rbx]`
    - `mov 20, [rax]`

## x86 Instruction Set
### Data movement instructions
- `mov`
- `push`
  - First, decrements register `RSP` by 8 (stack grows down)
    - RSP starts at maximum address (not technically, but we'll refine this later)
    - Gets smaller as we push values onto the stack
  - `push` operand onto stack
    - Same as using `mov`
  - sometimes you will see this as `pushq` (push quadword onto stack)
- `pop`
  - Pop top element of stack to memory or register, then increment stack pointer (`RSP`) by 8
  - Value is written to the parameter
    - `pop rax` - pops stack, stores in `rax`
    - `pop [var]` - pops stack, stores in address for `var`
      - Technically violates our "accessing memory twice" rule, but x86 allows this specifically
- `lea`
  - Load effective address
  - Place address of second parameter into the first parameter
```
lea rax, [var]           ; loads memory address of `var` into rax
lea rdi, [rbx + 4 * rsi] ; kinda weird: computes an address, references it, and dereferences it
                         ; shortcut for addition in a single instruction
```

If the arithmetic you need to do fits into the memory address scheme, you can do it in `lea` instead of using arithmetic instructions

### Arithmetic instructions
- add, sub
```
add <reg>, <reg>
add <reg>, <mem>
add <mem>, <reg>
add <reg>, <constant>
add <mem>, <constant>
```
- adds (or subtracts), storing result in first operand
- Similar restrictions as before:
  - Destination can't be a constant
  - Memory cannot be accessed twice in one instruction

`inc`, `dec` (increment/decrement by one)
```
inc <reg>
inc <mem>
```

`imul`
```
   imul <reg64>, <reg64> (or <mem>)
   imul <reg64>, <reg64> (or <mem>), <con>
```
`idiv`
- Divide 128-bit integer in RDX:RAX by operand
  - Remember to zero out `rdx` so you  don't get "some oddball 128 bit number"

## Logical instructions
`and, or, xor`
```
and <reg>, <reg>
and <reg>, <mem>
and <mem>, <reg>
```
e.g.,
```
and rax, 0fH
xor rcx, rcx
```
`xor`-ing a register with itself is a very fast way to zero it out

## Control instructions: jumps
- `jmp <label>`
  - go to instruction address specified by label
- `cmp`
  - Must be done prior to a conditional jump
  - `cmp <op1> <op2>`
    - `op1` can be a register or memory (variable)
    - `op2` can be a register, memory (variable), or a constant
    - You can't access memory twice
  - Sets the *machine status word*
- Conditional jumps: `j*` where `*` is some condition
  - Uses the machine status word, which was set via `cmp`
    - Holds info about the results of the last instruction
  - There are many values for `*`
  - Example: `je <label>`
    - Jumps when condition code "equal" is set
    - Others: `jne`, `jz`, `jg`, `jge`, `jl`, `jle`, `js`, etc.

## Control instructions: call and return
`call <label>`
- Subroutine call
- Pushes address of the *next* instruction onto the stack, then unconditionally jumps to the label
`ret`
- Subroutine return
- Pops the return address from the stack, then jumps to that address

### x86 Conventions
Two-argument instructions are `instr <dest>, <src>`

## Code in C and Assembly
**C/C++:**
```cpp
int n = 5;
int i = 1;
int sum = 0;
while(i <= n) {
	sum += i;
	i++;
}
```
**x86 Assembly:**
```asm
section .data
n    DQ 5
i    DQ 1
sum  DQ 0

section .text
loop:    mov rcx, [i]
         cmp rcx, [n]
         jg endOfLoop
				 add [sum], rcx
				 inc qword [i]
				 jmp loop
endOfLoop:
```
**Sections:**
- `.data`: declare variables, etc.
- `.text`: instructions that are executed

Note on `inc qword [i]`:
- For registers, it's obvious what size value we are incrementing based on the register name `rax` (is always 8 bytes)
- This is not the case for variables, which are just addresses in memory
- So we have to tell the assembler what size value we're incrementing if it's a variable

## Calling conventions
- What is a calling convention?
  - A set of rules/expectations between functions
    - How and where are parameters passed?
    - What registers does the calling function expected to be preserved?
    - Where should local variables be stored?
    - How/where should results be returned from functions?
- Why?
  - Separate programmers can share code more easily and develop libraries

### C Calling Convention
- Why C's calling convention?
  - It's important
  - Used with both C and C++ code
  - Can enable calling C library functions from assembly code
    - Or other languages, too

## C Calling Convention
- Uses hardware stack
- Stack *grows down*, toward the lower memory address
- x86 instructions used for calling conventions
  - `pop`
  - `push`
  - `call`
  - `ret`
- Using a stack for calling convention is implemented on most processors. Why?
  - Recursion!

- Answers to questions:
  - Parameters: passed in registers
    - If there are more than 6 parameters, then params 7 to *n* are placed on stack
    - If passing a large object, then it's placed on the stack (doesn't fit in a 64-bit register)
  - Registers: saved on the stack
  - Local variables: placed in memory on the stack
    - Or in registers if room is available
  - Return value: rax register
- Two sets of rules
  - Caller: the function which calls another function
  - Callee: the function which is called by another function

## Register usage
One register is used for return value: `rax`
Six registers are used for parameter passing: `rdi`, `rsi`, `rdx`, `rcx`, `r8`, `r9`
Two registers may be modified by the callee: `r10` and `r11`
- If the caller wants to save these values, they need to be saved by pushing them onto the stack
- Actually, these two have specific uses depending on the language, so they're often not used for this reason
Six registers may not be modified by subroutine callee: `rbx`, `rbp`, `r12` to `r15`
- If it wants to use them, the subroutine must push them onto the stack and restore them before returning
`rsp` should almost never be modified directly, as it points to the top of the stack

### Varying number of parameters
- Method overloading
  - `Foo::bar(int)` and `Foo::bar(int, float)`
  - Compiler will consider them different subroutines
- Default parameters
  - `Foo::bar (int x = 3)`
  - Also considered different subroutines
- Variable arguments
  - Coming soon...

### Variable # of arguments in C/C++

```cpp
double average(int num, ...) {
	// compute the average of the arguments
}
```


*Is this actually used?* Yea, in `printf` which takes a variable number of arguments for its format parameters


## Caller rules
- "Prologue"
  - Tasks to take care of before calling a subroutine
- Call the subroutine with the `call` opcode
- "Epilogue"
  - Tasks to complete after call returns

## Caller rules / responsibilities
- *Before* calling the function
  - Save registers that might be needed after the call (r10, r11, or param registers if applicable)
  - Place parameters in registers / on stack
    - rdi, rsi, rdx, rcx, r8, r9
    - Then push extra parameters on stack
- *Call* the function
  - `call` instruction places return address on stack
- *After* the called function returns
  - Remove parameters from stack (if applicable)
  - Restore saved registers (if applicable)

### Caller rules - "prologue"
1. Caller-saved registers
  - These registers must be saved if the caller wants their values preserved.
  - r10, r11, and registers used for paremeters
2. Parameters
  - First 6 are passed in through registers
  - Params 7 to n pushed in reverse order (last parameter first) onto stack
3. Call the subroutine
  - Use the `call` instruction
    - Pushes the return address onto the stack and branches to the subroutine

### Caller rules - "epilogue"
"Cleanup time"
- Remove parameters
  - Parameters pushed onto the stack must be removed
  - What do we do with them?
- Return value
  - If any, held in rax
- Restore caller-saved registers
  - Pop them off the stack, restore their value

## Caller rules example
```asm
push rdi ; rdi will be a param, so save it
mov rdi, rax ; put first param in rdi
mov rsi, 123 ; put second param in rsi
mov rdx, [var] ; put third param in rdx

call myFunc ; call function

pop rdi ; restore saved rdi value

; return value of myFunc is now available in rax
```

## Callee rules
**Prologue:**
1. Allocate local variables
  - Make space on stack (decrement stack ponter)

*This is one of the only times we directly modify the stack pointer.*

***PSA: You might see:***
```
push rbp
mov rbp, rsp
...
pop rbp
```
Need to compile with `-fomit-frame-pointer`

**Prologue, continue:**
3. Save callee-save registers
  - rbx, rbp, r12 to r15
  - Only need to do this if you want these extra registers

Then, perform the body of the function

**Epilogue:**
1. Return value saved to rax
2. Restore callee-saved registers
  - Pop from stack (in reverse order)
3. Deallocate local variables
  - `add rsp, 8` where `8` is replaced with size of your stack frame (size of allocated locals)
4. Return
  - `ret` - make sure you properly manage stack so that ret doesn't pop an invalid address

```
myFunc:

sub rsp, 8 ; room for 64-bit local var
push rbx ; push 8 byte (rsp -= 8)
push rbp ; push 8 byte (rsp -= 8)
; [rsp + 16] now represents location of local var

; load parameters into rax, rbp, rbx
mov rax, rdi
mov rbp, rsi
mov rbx, rdx

; put rbx into local var
mov [rsp+16], rbx
; add rbp to local var
add [rsp+16], rbp
; copy into return value
mov rax, [rsp+16]

pop rbp ; restore value of rbp
pop rbx ; restore value of rbx
add rsp, 8 ; deallocate local vars
ret ; pop top value from stack and jump
```

## Activation Records
- Every time a subroutine is called, a number of things are potentially pushed onto the stack:
  - Registers
  - Parameters
  - Local variables
  - Return address
- All of this is called the *activation record*

### Memory management
- Binary program takes up a fixed amount of space
- Two types of memory that need to be handled:
  - Dynamic memory (via `new`, `malloc()`, etc.)
    - Store on the *heap*
  - Static memory (on the *stack*)
    - Keeps activation records

Compilers can optimize early for static memory: they know exactly how large the stack frame for a given function should be

Binary program starts at the beginning of the 2^32 = 4 Gb of memory
Heap starts right after (say, address 10,000 if your binary is 10kb)
Stack starts at the end of this 4 Gb of memory, and grows backward
As a program progresses, they both grow toward the middle
  ...and never shall meet :(

Operating systems generally limit how far the stack can grow
- Consider a recursive function that never reaches a base case
- Keeps pushing activation records to the stack, over and over.
- Will eventually have a *stack overflow*

#### Example
```c
void security_hole() {
  char buffer[12];
  scanf("%s", buffer); // read into buffer
}
```
Stack looks like this:
```
+------+------+--------+----------+
| rsi  | rdi  | buffer | ret addr |
+------+------+--------+----------+
```
- Caller backed up return address
- Callee allocated buffer
- Callee backed up some registers

Works great if we're writing anything less than 12 bytes
...but `scanf` doesn't care about that

If we enter more than 12 bytes, then we have a *buffer overflow*
We can write somewhere else in the stack. Call that *stack smashing*

**Lucky case:**
- We overwrite the return address. When we call `ret`, we jump to an invalid address, and our life is gracefully yet forcefully terminated by the operating system.

**Unlucky case:**
- We overwrite the return address to jump to attacker-supplied code. Uh oh, RCE!

Note: each program *believes* it has full access to the machine memory
- It doesn't.

### x86 Code
**Absolute Value:**
```cpp
long abs(long x) {
  if(x < 0)
    x = -x;
  return x;
}
```
**Corresponding x86:**
```s
abs:
  mov rax, rdi ; store arg in rax
  cmp rax, 0 ; compare with 0
  jge end_of_proc ; if >= 0, just ret
  neg rax ; else, negate
end_of_proc:
  ret
```
**Corresponding x86 generated by clang:**
Output of `clang++ -m64 -S abs.cpp -o abs_non_intel.s` is using AT&T syntax
We learned Intel syntax
So we use:
`clang++ -m64 -mllvm --x86-asm-syntax=intel -S -fomit-frame-pointer test_abs.cpp -o test_abs.s`
