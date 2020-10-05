# trees2
# Notes 10.05.2020 - notes

## Recursion
Let's sum the numbers from 1 to `n`.

```cpp
// iteratively

int sum(int n) {
	int s = 0;
	for(int i = 1; i <= n; i++) {
		s += i;
	}
	return s;
}

// recursively

int sum(int n) {
	if(n == 0) {
		return 0;
	} else {
		return n + sum(n-1);
	}
}
```

### Recursion needs:
- A way to make the problem "simpler"
  - Reduce to subproblem
- A base case when recursion should terminate
- A way to terminate

### Factorial via recursion
```cpp
unsigned long fact(unsigned long n) {
	if(n == 0) // base case
		return 1;
	else // recursive case
		return n * fact(n-1);
}
```

### Recursion: pros and cons
**Pros:**
- More natural way to think of some problems 
- Some problems work well with recursion—not so much with iteration
**Cons:**
- Invoking a subroutine at each step slows performance

### Toy fibonacci
```cpp
unsigned int fib(unsigned int n) {
	if(n <= 1)
		return 1;
	else
		return fib(n-1) + fib(n-2); // horribly inefficient, no memoization
}
```

### Recursion on trees
- Call the function on left and right subtree
- Detecting end is if your node is null
- Cannot easily traverse a tree through iteration

### Recursion on lists
- Check head element, call func on head's next
- Smells of tail end recursion

### Tail recursion
**Exhibit A:**
```cpp
unsigned int sum(unsigned int n) {
	if(n == 0)
		return 0;
	else 
		return n + sum(n-1);
}
```
We have to modify the return value to generate our result.

**Exhibit B**:
```cpp
unsigned int sum(unsigned int n, unsigned int s) {
	// s = "the sum so far"
	// n = remaining counter
	if(n == 0) 
		return s;
	else
		return sum(n-1, n+s);
}
```
The result of the recursive call is returned without modification.

**Why tail end recursion?**
- Compilers recognize tail recursion and convert it into a for loop
  - Then optimize it so there's no penalty to calling subroutines
- ...but not all problems can be solved using tail recursion

## Red-Black Trees
- AVL Trees can guarantee logarithmic time because the difference in height between the right and left subtree is guaranteed to be at most 1.
  - Balance!
- Here's the thing:
  - Nobody uses AVL trees.
- People use Red-Black trees all the time.
- Every node is either red or black.

### Properties
1. A node is either red or black.
2. The root is black.
3. All leaves are black.
  - The leaves may be the `NULL` children.
4. Both children of every red node are black.
  - Any red node must have a black parent.
5. Every simple path from a node to any descendant leaf contains the same number of black nodes.
  - Counting or not counting the `NULL` black nodes; doesn't matter so long as you're consistent

#### Consequence of these properties
- If you have *n* black nodes, the height of the tree is at most *2n* 
  - If you have 2 black nodes, the longest possible path is black-red-black-red

#### Insert
- Insert the node as for normal BST
  - Color it Red

**5 possible cases:**
1. The new node is the root node
  - It must be painted black. This adds a black node to every path in the tree, so prop 5 still holds.
2. New node's parent is black.
  - Property 4 is not invalidated
  - Property 5 is not invalidated
    - The new node's children are black, but that's the same number of black nodes as the node it replaced.
3. Parent and uncle are red
  - Change grandparent to red
    - And parent and uncle to black
  - Property 5 still holds
  - However, the grandparent may threaten property 2 or 4
    - Recursively do this on the grandparent
4. Parent is red, uncle is black, N is the right child of parent
  - Perform a left rotation on P and N
    - Prop 4 is still violated
  - Treat it as case 5 (where P is left-child of N)
5. Parent is red, uncle is black, N is left child of P
  - Perform a right rotation on P and G
  - Satisfies 4 and 5

**There are a LOT of red-black removal cases.**
Guaranteed logarithmic runtime
  ...and a little bit faster than AVL
  Doesn't show up in the complexity analysis

# Hashing
We've seen list-like data structures
  Typically O(n) or O(1)
Seen tree-like datastructures
  Typically O(logn) or O(n)

**Is there anything that finds faster?**
Best binary searches are Omega(logn)

We can do better: (almost) constant time with hash tables
- Standard set of operations: find, insert, delete
  - ...but no ordering property
  - Can't findMin or findMax

## Hashtables
Hash tables store key-value pairs
Each value has a specific key associated with it, and keys and values do not necessarily need to be the same type

Examples:
- Definitions in a dictionary
- UVA email redirects
- Anything that can be stored in a tree
  - User id and ID number pairs
  - User id and user attribute pairs

**Hash table:**
- Backed by fixed array of some size, usually a prime number
  - Should be larger than the number of elements we anticipate to store
- Hash table
  - Given a key space: K
  - Use a hash function hash: K -> {natural numbers}
  - Use output of hash function to choose array index for storing a value

A hash function takes in a "thing" and returns an unsigned integer value.
We mod the hash integer by the table size to get an index into the table

**Required properties of hash functions**
- Deterministic
- Fast
- Evenly distributed

Only the first one is required for correctness, but the other two are required for efficiency

Example hash functions on cs2150 students:
- hash(birthday) = (year / 10) % 10
  - Deterministic
  - Fast
  - Evenly distributed? No
    - All the students born in the 90s or 00s
- hash(birthday) = year % 10
  - Deterministic
  - Fast
  - Evenly distributed? Better, not there
    - Most students born in a span of 4-5 years
- hash(birthday) = year % 12
  - Deterministic
  - Fast
  - Evenly distributed? Pretty good
    - Buckets 1 and 2 get more students 
- hash(birthday) = day % 10
  - Deterministic
  - Fast
  - Evenly distributed? Decent
    - Pretty good
