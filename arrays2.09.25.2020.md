# arrays2
# Notes 09.25.2020 - notes

## The sets O(g), Theta(g), omega(g)
### Formal defn.
- f and g functions from nonnegative integers into the positive real numbers
- For some real constant c > 0 and nonnegative integer constant n_0,
- O(g) is the set of functions f s.t.
  - f(n) <= c * g(n) for all n >= n_0
- Omega(g) is the set of functions f s.t.
  - f(n) >= c * g(n) for all n >= n_0
- Theta(g) = O(g) intersect Omega(g)
  - This is equivalent to f(n) = c * g(n) for all n >= n_0

We can make plausible lower/upper bounds for many functions, but they might not tell us a lot about the actual runtime
  - Say some function grows like f(n) = n
  - f is O(n^2), Omega(logn)
    - These values diverge really quickly and stop being super useful

So big theta provides us a *tight bound*: a more exact representation of the worst-case runtime


### Examples

Is n in O(n^2)?
  - Yes: c = 1, n_0 = 2
  - n <= 1 * n^2 for all n >= 2

Is 10n in O(n)?
  - c = 11, n_0 = 0
  - Yes: 10n <= 11n for all n >= 0

Is n^2 in O(n)?
  - Pick any c, eventually, n^2 >= cn

### Questions
Given F in O(H) and G not in O(H), which are true?

1. For all positive integers m, f(m) < g(m)
2. For some positive integer m, f(m) < g(m)
3. For some positive integer m_0, and all positive integers after it, f(m) < g(m)

**Answer:** 2 and 3

## Theta ("order of")

Intuition: the set theta(f) is the set of functions that grow exactly as fast as f
Definition: f(n) is in theta(g(n)) if and only if:
  f is in omega(n)
  f is in O(n)

*Note*: constants c and n don't have to be the same


## Little-oh
f(n) is in o(g(n))
o(g) = O(g) \ Theta(g)
- f grows strictly less than g(n)

## Little-omega
Lower bound that is not a tight lower bound

### Functional properties

- Reversing big-oh: f in O(g) <=> g in Omega(f)
- Adding: O(f+g) = O(max(f,g))
- Transitive: if f is O(g) and g is in O(h), then f is in O(h)
  - All of our relations are transitive
- Reflexive:
  - f is always Theta(f)
  - f is always O(f)
  - f is always Omega(f)
- Symmetric: if f is Theta(g), then g is Theta(f)
- Theta is an equivalence relation on the functions
  - Each set theta(f) is an equivalence class

Exponentials always grow faster than polynomials

- O(c) = constant
- O(logn) = log
- O(n) = linear
- O(nlogn) = log-linear
- O(n^2) = quadratic
- O(n^3) = cubic
- O(c^n) = exponential

### Does order class matter?
Not for small inputs.
But for larger, real problems, order class is a very practical consideration

### Running time calculations
- For loops
  - Number of times the for loop runs times the total running time of the loop body 
- Nested for loops
  - Analyze from inside to out
    - Runtime of statement * product of sizes of loops
- Consecutive statements:
  - Max runtime of individual statements
- If else,
  - Time for test plus longer of the branch runtimes

### Constant time
- The time taken to complete your operation doesn't change with input size
  - Getting vector size
  - Linked list insert or delete (at known end)
  - Indexing into an array/vector

### Logarithmic time
- Applicable when search space is repeatedly halved
  - Binary search
  - Search or insert in binary tree

### Linear time
- Do a single operation on each element in some datastructure
  - Printing a list
  - Finding an element in an unsorted array
  - Finding an element in a sorted or unsorted linked list
  - Doubling the backing array underlying a vector

### Log-linear time
- Typically occurs when you're doing a linear number of steps, but each step takes logn time
  - Fast sorts: merge sort, heapsort
  - Quicksort on a good day

### Quadratic
- Nested for loops
  - Slow sorts
  - Quicksort

### Exponential
- When you have to try every possible combination
  - Cracking a binary combination lock
  - TSP
  - Boolean satisfiability
