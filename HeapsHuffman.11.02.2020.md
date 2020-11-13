# HeapsHuffman
# Notes 11.02.2020 - notes

# Heaps (Priority Queues) and the Huffman Coding

### Motivation
- "There are times when some things are more important than others."
  - Operating system scheduler
  - Bandwidth on network router
    - Limited bandwidth, but want to prioritize some traffic
    - VoIP gets prioritized

### Priority Queue ADT
- Operations
  - insert
    - with *priority*
  - findMin
    - finds the minimum element
  - deleteMin
    - finds, returns, and removes the minimum element

**Potential realizers:**
- Unsorted array
- Unsorted linked list
- Sorted array
- Sorted linked list
- Binary search tree
- AVL or red-black tree
- Hashtable

Desideratum:
- constant-time `findMin`

**Binary Heap**: *one* way to implement a priority queue
Binary heap is a binary tree (but not a BST), with a different:
- Structure property
- Ordering property

A *perfect* binary tree has all leaf nodes at the same depth, all internal nodes have 2 children
Removing an arbitrary node from a perfect binary tree does not preserve its perfection

A *full* binary tree is a binary tree in which every node has exactly zero or two children.
  (No restrictions about the level of children, just restrictions on whether

A binary heap is an *almost complete* binary tree, which is a binary tree that is completely filled, except possibly the bottom level, which is filled left to right.

Almost complete binary trees can be stored in an array

From node *i*:
- Left child: 2*i*
- Right child: 2*i* + 1
- Parent: floor(*i*/2)

All of these indices can be computed readily in assembly (all left/right shifts and increments)

*Even though we will diagram them with trees, we will almost always back them with arrays*

### Heap ordering property
For every non-root node X, the key in the parent of X is less than (or equal to) the key in X. The tree is *partially* ordered.

So the min is always at the root

`findMin` is constant -- just look at the root node
`insert` -- "percolate" up
`deleteMin` -- "percolate" down

#### Insert
- Insert at rightmost of bottom row -- in the array, first empty spot
- Repeatedly compare with parent, swap if misordered
- Worst-case: `logn` runtime
- Expected runtime: *constant*
  - Half of the nodes are leaves, so half of the inserts will only move up one level
  - Quarter are one level above leaves, so a quarter of inserts will only move up two levels
  - One eighth require moving up three levels
  - etc...
  - 1/2 * 1 + 1/4 * 2 + 1/8 * 3 + … = 2
  - **Important:** This is not amortized: if you're always inserting a minimum, you'll always trigger worst case

#### Delete
- Remove root node, take rightmost leaf and place it at root
- Repeatedly replace new root node with smaller of its children
- Worst-case: `logn` runtime
- We always need to swap with the smaller of the two children:
  - Consider:
```
   25    <--- Needs to be percolated!
  /  \
 10  20

   20
  /  \
 10  25  <--- If we swap with greater child, 20 > 10, and the ordering property is violated.
```


## Other heap operations
`decreaseKey(processID, amount)`: decrease priority value, making a process more important, percolate up
`increaseKey(processID, amount)`: increase priority value, making a process less important, percolate down
`remove(processID)`: remove a process, move to top, then delete
  - `decreaseKey(processID, -infinity)`
  - `deleteMin()`

Worst case runtime for all of these is `Theta(n)` because we have to search and find the element with a given `processID`
(We can optimize this, because perhaps process records have pointers to their entries in the binary heap)

## Heapsort
- Insert `n` elements, then remove `n` elements
- Each one has an insertion time of `logn`
- And then removal time of `logn`
- Therefore `Theta(nlogn)`
- But this is an unstable sort, so it's not used as often as mergesort

# File Compression
### Motivation
- Disk space and network bandwidth cost money
- Transfer happens faster when there's less info

What is a file, anyway?
- "Named collection of information" -> could be c++ source, application executables, images, audio, etc.

Which formats need to be the same, byte-for-byte, when we decompress?

```
X --> [ ENCODER ] --> Y --> [ DECODER] --> X'
```
**Lossless compression:** X = X'
**Lossy compression:** X != X'
**Compression ratio:** |X| / |Y| ( size of x / size of y)

## Lossy compression
Some data is lost, but not "noticeable" data
**Standards:**
- JPEG - lossy image compression
- MP3, Ogg vorbis, AAC, etc. - lossy audio
- MPEG, as well as most video codecs - lossy audio and video

Compression ratios of 10:1 are possible

JPEG has variable quality
- Abuses a feature of the human eyes to "throw out" information we won't notice
- Low frequency information is hard to detect, we're much more sensitive to high frequency information (edges, stripes, many colors)

## Lossless compression
- No data is lost
- Standards:
  - Gzip, unix compress, zip, Morse code
  - PNG image formats
  - Run-length encoding (RLE)
- Compression ratios more like 4:1

## Lossless compression of text
- ASCII - 8 bits per character
- Example: "hello there"
  - = 88 bits

### Huffman coding
- Uses frequencies of symbols in a string to build a prefix code
- The more frequent a character is, the fewer bits we'll use to represent it
- **Prefix code:** no code in our encoding is a prefix of another code

We build a binary tree using the code
`0` is left, `1` is right
Prefix code provide directions to our leaf
Our tree is a full binary tree (Node x => Leaf x OR (HasLeft x AND HasRight x))
Internal nodes are always empty
Leaf nodes represent characters

Pseudocode for decoding:
```
while not done:
  node = root
  while True:
    if message.next() == '1':
      node = node.right
    else:
      node = node.left
    
    if node.right == None and node.left == None:
      break
  print(node.char)
```

"Cost" of a Huffman code: Sum(frequency of character * length of code for character)

- ASCII: 8 bits per character
- "Straight" encoding of four symbols: 2 bits per char
- Our Huffman code: 1.85 bits per character

#### Compression
1. Determine frequencies of characters stored in the source file
  - Read source file, store character frequencies in a *min-heap*
2. Build a tree of prefix codes that determines the unique bit codes for each character
3. Write the prefix codes or code tree to the output file
4. Re-read the source file and for each character, write its prefix code

We must write the prefix code/tree to the output file with the encoded text

## Encoding
- First file pass: count distinct characters and their frequencies
- Turn each character into a `HuffmanNode` (char + frequency + L/R pointer)
- Place each `HuffmanNode` into a min-heap
- While heap has more than one element:
  - Grab minimum element, twice
  - Create new `HuffmanNode` with left pointer to one node and right pointer to other, frequency equal to sum of each node's frequency
  - Put new `HuffmanNode` back into heap
- Convert your Huffman tree to a mapping from characters -> prefix codes
- Second file pass: map each character to its prefix code

Yields a few desirable traits:
- Least frequent characters pulled first, placed at bottom of tree ( = longest codes)
- Most frequent characters pulled last, placed near top of tree ( = shortest codes)

Now, we have a full binary tree where the paths to each leaf represent a code for that leaf's character
How do we convert to a mapping?


# Back to priority queues
- Heaps are distinct from priority queues; it just so happens that heaps are very good realizers of priority queues

## Time complexities
- Insert: `logn`
  - Average, however, is `O(1)`
- Delete: `logn`
- Create heap from unsorted vector: `nlogn`

### Creating heap from an unsorted vector
```cpp
binary_heap::binary_heap(vector<int> vec) {
	heap = vec;
	heap.push_back(heap[0]); // hack: we don't use 0th index, so make the 0th element the last one
	heap[0] = 0;
	for(int i = heap_size / 2; i > 0; i-- ) {
		percolateDown(i); // NOTE: we only percolate down i/2th .. 1st elements, because the last size/2 elements are leaves and have nowhere to percolate
	}
}
```
Worst-case: `O(nlogn)`
