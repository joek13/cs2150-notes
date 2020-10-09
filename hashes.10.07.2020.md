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
| - | - |
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

Probing strategies
1. Linear
2. Quadratic
3. Double hashing

If we have a collision, just check the next cell until an open one is found
Make sure we "loop around" if we hit the end of the table

## Conflict resolution: Linear probing
- Check hash(k), then hash(k) + 1, hash(k) + 2, …
- Avoids the (memory) overhead of linked lists

### Aside: what makes hash tables work well?
- Hash function
- Conflict resolution
- Load factor

| Index   | Value   |
| ------- | ------- |
| 0       |         | Insert: 4, 27, 37, 14, 21
| 1       | 21      | (where hash is mod 10)
| 2       |         |
| 3       |         |
| 4       | 4       |
| 5       | 14      |
| 6       |         |
| 7       | 27      |
| 8       | 37      |
| 9       |         |

Just tries cells one after the other until the spot is filled.

As long as the load factor is less than 1, linear probing will *eventually* find a spot

**Issues with linear probing:**
- Creates *clusters* of values that have linear runtime near the cluster
  - Created if you have an unevenly distributed hash function, or maybe just bad luck

*Load factor's relationship with collision*
P(collision) is proportional to load factor

Trade-off for load factor: runtime vs. memory
- As your load factor goes down, the chance of collision goes down (faster runtime), but the memory space of your backing array goes up
- Higher load factor = less memory, longer runtime (more collisions)

**Find:**
To find *x*:
- Compute hash( *x* ), look at that spot
  - If it has *x*, you found it
  - If it has some *y* != *x*, keep going (look at next cell, wrapping around)
  - If it's empty: stop! *x* is not in the table


**Delete:**
Complicates things. If we delete something, how do we know if an empty cell is the result of deletion, or of nothing ever having been there?

*Addressed in about 15 minutes.*


### Linear probing
- With all open addressing schemes, we examine ('probe') the cells in the order
  - p_0(k), p_1(k), …
  - Where p_i(k) = (hash(k) + f(i)) mod table_size
- With linear probing, f(i) = i
  - After checking hash(k), we check hash(k) + 1, hash(k) + 2, …

#### Problems with linear probing
- Primary clustering 
  - Large blocks of occupied cells
  - As table fills, increased # of attempts required to solve collision
  - "Holes" when an element is removed
  - When do we know to stop looking?

### Quadratic probing
- With all open addressing schemes, we examine ('probe') the cells in the order
  - p_0(k), p_1(k), …
  - Where p_i(k) = (hash(k) + f(i)) mod table_size
- With quadratic probing, f(i) = i^2
  - After checking hash(k), we check hash(k) + 1, hash(k) + 4, hash(k)+9, …
- In general, you move out of clusters much quicker

### Double hashing
- With all open addressing schemes, we examine ('probe') the cells in the order
  - p_0(k), p_1(k), …
  - Where p_i(k) = (hash(k) + f(i)) mod table_size
- With quadratic probing, f(i) = i * hash_2(k)
  - We have to define a secondary hash function
  - After searching spot hash(k), look in:
    - hash(k) + 1 * hash_2(k)
    - hash(k) + 2 * hash_2(k)
    - hash(k) + 3 * hash_2(k)

*Issue*: hash function *cannot* be zero
  - k * hash_2(k) is always 0, so we keep trying the same spot endlessly

#### "Double hashing thrashing"
*Issue 2:* What if we get into a cycle? i.e., table is size 10, hash_2(k) = 2, first spot = 8
  - we try 8, 0, 2, 4, 6, 8, 0, 2, …
  - Never try the odd-numbered spots
  - We get in infinite cycles when the secondary hash is a factor of the table size
  - Solution?
    - Make the table sizes prime-numbered

### Why prime-numbered tables?
- Avoids thrashing
  - The only factors of a prime number *p* are 1 and *p*
    - 1 is fine, that's linear probing
    - if hash_2(k) = p, then we have a zero hash (after modding), which is fixed back to 1
- Provides better distribution of the hash keys
  - Prime number theory justification that our peabrains can't understand
- A prime number table size doesn't remove the need for a good hash function
  - Just helps compensate for practicalities

## Rehashing
*Problem:* what happens when the table gets full?
- Running time for operations begins to increase
- *Solution:* create a bigger table and hash all the items from the original table into the new table
  - The position in the table is a function of table size, so we have to *rehash* every value
  - This means we have to re-compute the hash value for each element and insert it into the new table

What is the time complexity? Worst case, the inserts into the new table are linear. And we do n of them. So rehashing is Theta(n^2).
  (Oof)

- Although it's not likely to ever run that slow

## Removing an element
- How to handle this?
  - Rehash upon each delete, which is *very* expensive
  - Put in a 'placeholder' or 'sentinel' value
    - But the table gets filled with these rather fast
      - The PSL is still high for those inserted towards the end of clusters, and we can't reclaim their spot
    - Perhaps rehashing after a certain number of deletes
  - Disallow deletes entirely
- Hash tables are not an ideal data structure if you need to perform a lot of deletions

## Hashing: MD5
- MD5: message digest 5
- Given a string (or file contents, etc.) generate a 128-bit hash
  - Typically, an MD5 is always written in hex
- Can be used as a key
- (Was) good for checking if a download completed successfully

## Can you reverse MD5?
- Technically, no...
  - By the pigeonhole principle, there are several keys that hash to the same value
- But if a password is stored by its MD5 hash...
  - There are enough online hash libraries that you can find at least one password that hashes to that value 
