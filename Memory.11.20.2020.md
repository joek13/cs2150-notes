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

#### Heap management
- In comparison to stack, typically used for dynamic allocation
- Heap grows up after program binary in memory
- Compiled into the program's code is a heap directory management routine
  - (part of `libc`)
  - Adds extra time to a `new` or `malloc()`
- Allocates memory in two ways:
  - Fixed-size-blocks - most common on embedded systems
    - A free list of available blocks is kept, and the appropriate number are allocated
    - Available blocks kept in a bitmap
      - But this takes space... so small blocks can give you really large directory structures
    - Also, larger blocks waste space
  - Buddy blocks - more common on personal computers
    - Blocks are divided into powers of 2, so the memory takes up the next highest power of 2 bytes
      - All of memory is split into pairs
      - When we want to allocate memory, just request the smallest power of 2 that is larger than the amount needed


##### What happens when we malloc?
- A subroutine is invoked
- The OS is consulted, if necessary, to allocate a page of memory
  - context switch to OS
- The heap directory is examined
  - New space somehow determined
- Subroutine returns reference to new memory region

### Why do we care about memory?
- Memory hierarchy, locality
  - Computer architecture

#### Memory hierarchy
- CPU registers
  - 1 access per cycle
  - 3 * 10^9 accesses per second
  - 1 Kb total storage
- Cache - much smaller than main memory, but much faster
  - SDRAM: 10ns
  - 10^8 accesses per second
  - Multiple levels possible
  - Higher levels have more storage, but are slower
  - 1 Mb total storage
  - If the value we need is already in cache, we get a cache hit and can avoid a slow main memory read
  - LRU cache
    - Whichever page was least recently used gets evicted from cache
    - Might evict from L1 into L2
    - Reads happen synchronously, writes can happen asynchronously
- Main memory
  - ~100 cycles per read
  - A lot of storage, but comes at a cost
  - what if we run out of memory?
    - We can initiate a page swap, using disk for memory
- Disk
  - ~1,000,000 to 10,000,000 cycles per access
  - Swapping is extremely slow

#### Definitions (slightly informal)
- Cycle
  - For our purposes: the time it takes to execute a "simple" instruction (e.g. add eax, ebx)
- Memory access time (latency)
  - time it takes to access/read memory
- Memory cycle time
  - Time it takes to write to memory
- Cache hit: address requested is in cache
- Cache miss: address requested not in cache
- Cache page size/chunk size/line size: number of contiguous bytes that are moved into the cache at one time

#### So what?
- How do we optimize for the unique strengths of cache?

#### Locality
- Temporal locality
  - Locality in time
  - If an item is referenced, it will tend to be referenced again soon
- Spatial locality
  - Locality in space
  - If an item is referenced, items whose addresses are close by will tend to be referenced soon

**Example:**

```cpp
int sum = 0;
for(int i = 0; i < n; i++) { // temporal locality: i is accessed many times in the same general timeframe
  sum += a[i];               // spatial locality: area near the array is accessed many times
}
```

#### Consequences
- Accessing memory "with the grain"–in row-major order–is much faster than in column-major order
  - Around 50x faster!!
- If you access an array in row-major order, each access will either be on the same cache page or on the next consecutive cache page

#### Trends
- Over time, processors have been getting much, much faster
- ...but memory has stayed roughly the same

## String functions - a high-level overview
C-strings are pointers to characters
Each byte is an ASCII encoding of the character
Last byte is binary zero—null terminator

## C-string functions
- Concatenating two strings is *annoying*:
- Calculate the length of both, allocate new string, copy both in, and deallocate old strings
