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
