# Memory
# Notes 11.20.2020 - notes

## Static/dynamic memory allocation
- Static: space required is known before program starts (at compile time)
- Dynamic: space required is not known before the program starts
  - Can be placed on either the stack or the heap
  - But most often, the heap

## Memory in C
- Stack
  - Managed by compiler automatically
  - Lifetime determined by program scope
  - Can't outlive procedure return (scope)
  - Address space grows down from top
- Heap
  - Handled by programmer explicitly
  - Lifetime controlled by programmer
  - Lives until freed
  - Address space grows up from bottom

## Memory layout

- Stack grows from top down
  - Each stack frame subtracts from the stack pointer, etc.
- Heap grows from bottom up
  - Starts after binary program in memory layout

Imagine some issues:
- Infinitely recursing function
  - Say a function just calls itself, without using any locals
  - Execution just involves allocating stack frame after stack frame
  - Eventually, stack overflows … but this takes a while.
  - And honestly, no problem is gonna need more than 100Mb of stack space. So this is enforced.

## Memory allocators
- Known size, scoped lifetime: local variable declarations
- Unknown size, scoped lifetime: `alloca()` (rarely used, variable-sized stack allocation)
- Known size, unlimited lifetime: global, static variable declarations
- Unknown size, unlimited lifetime: `new` and `malloc()`

## `malloc`
`void *malloc (size_t size)`
- Returns an untyped pointer (can point to anything)
- Returns an address that is the location of at least `size` bytes of previously unused memory, and reserves that space
- Returns `NULL` if there isn't enough space
- E.g., allocating a C string
  - `char *s = (char *) malloc(sizeof(*s) * n);`
  - `sizeof(*s)` is the size of a character

