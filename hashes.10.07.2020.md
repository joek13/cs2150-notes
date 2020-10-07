# hashes
# Notes 10.07.2020 - notes

### Recall: hashtable overview
Take our key, plug it into a hash function that assigns it to an integer, and then put it into the table at `(hash(key) % tableSize)`

#### Properties of a hash function
1. Deterministic
  - Same input? Same output.
2. Fast
  - A slow hash function defeats the purpose of using a hashtable.
3. Evenly distributed

## Keys
How can we hash the keys if they can be anything?

Bloomfield suggests:
- Lookup table
  - But lookup is slow
  - And we have to know every possible key beforehand

### Example
Consider the key space of integers
Table size of 10

`hash(k) = k % 10`

Insert: 7, 18, 41, 34

| Index | Value |
|-------|-------|
| 0     |       |
| 1     | 41    |
| 2     |       |
| 3     |       |
| 4     | 34    |
| 5     |       |
| 6     |       |
| 7     | 7     |
| 8     | 18    |
| 9     |       |

**Issues with table size:**
What if we just make table size = |key space|?
...because often we don't actually use every key

Imagine creating a UVa student hash collection that hashed by SSN. There aren't 999,999,999 + 1 students, so while insertion is fast, the table is way larger than in needs to be.

### Another example
Key space: integers
Table size: 6
`hash(k) = k % 6`

Insert: 7, 18, 41, 34, 12

| Index | Value      |
|-------|------------|
| 0     | 18, **12** |
| 1     | 7          |
| 2     |            |
| 3     |            |
| 4     | 34         |
| 5     | 41         |

There's a *collision*.

### General hash functions
Hash function is a function from Key -> [0, *m*-1]
  - Where *m* is table size

### Hash functions
A *perfect* hash function has:
- No blanks
  - (Is surjective)
- No collisions
  - (Is injective)

A perfect hash function is bijective

### String hash functions
Key space: strings
A string *s* is made up of characters *s_i*

```py
def hash(s):
  hashval = 0
  for i,c in enumerate(s):
    hashval += ord(c) * (37 ** i) # convert char to int

  return hashval
```

### Hash functions
- Should return unsigned ints

### Collision resolution
- What happens when two keys hash to the same spot?

Two primary ways:
1. Separate chaining (make each spot in the table a bucket or collection)
2. Open addressing
  - Linear probing
  - Quadratic probing
  - Double hashing

### Separate chaining
All keys that map to the same hash value are kept in a bucket
The bucket is another data structure, typically a linked list
  - Yeah, we could use more complicated data structures, but a good hash table will never exceed 2 or 3 elements in a given bucket.

What is the worst case runtime of a hash table?
  - Linear.

Super contrived, but theoretically worst case: all inserted values hash to the same spot. 

Lesson: another case where Big-Theta doesn't give a super great characterization of the datastructure
When the typical case is *nothing* like the worst case

### Find
Defn: the *load factor* lambda of a hash table is the ratio of the number of elements divided by the table size
For separate chaining, lambda is the average number of elements in a bucket
- Average time on unsuccessful find is proportional to lambda
- Average time on successful find proportional to (lambda / 2) + 1

### Load factor
- How big should we make the hash table?
- Possible sizes for hash table using separate chaining
  - lambda = 1
    - Make hash table be the number of elements expected; average bucket size is 1
    - Also, make it a prime number
  - lambda = 0.75
    - Java's HashTable but can be set to another value
    - Table will always be bigger than the number of elements
      - Reduces chance of a collision
    - Middle ground
  - lambda = 0.5
    - Double the spaces as you expect
    - Fewer collisions but a lot of memory

The "hopeful" case: Theta(1)
  Typically: Theta(1)-ish

## Open addressing
**Motivation:** can we avoid the overhead of all these linked lists?

Proving strategies
1. Linear
2. Quadratic
3. Double hashing

If we have a collision, just check the next cell until an open one is found
Make sure we "loop around" if we hit the end of the table
