# trees
# Notes 09.28.2020 - notes

## Trees
- If we have a good list implementation, do we need any other data structures?
- For computing: no
  - Everything is reduced to memory, which is essentially just a list
- For thinking: yes

### List limitations
- Each element only has direct relationship with predecessor and successor

### Trees
- Good for describing complex hierarchical relationships
  - Descendants, ancestors, etc.
- Can be thought of as a list where each node can have two (or more) successors

#### Terms
**Root**: a node with no parent, can only be one
**Leaf**: a node with no children
**Siblings**: two nodes with same parent
**Height of a node**: Length of the longest path from that node to a leaf
  - All leaves have height 0
**Depth of a node**: Length of the longest path from root to node
**Path**: sequence of nodes n_1, n_2, …, n_k such that n_i is parent of n_i+1 for 1 <= i <= k
  - We only traverse parent-to-child
**Length**: number of edges in the path (# of nodes - 1)
**Internal path length**: sum of the depths of all the nodes
  - Useful for calculating the average depth of a tree

#### Tree uses
- Parse tree / AST
- Computer filesystem
- Genealogy trees
  - Actually pretty bad because of ~incest~


#### Tree traversal
- Pre-order: root, left, right
  - Print node, then preorder(left), then preorder(right)
- In-order: left, root, right
  - Print inorder(left), root, inorder(right)
- Post-order: left, right, root
  - Print postorder(left), postorder(right), root

### TreeNode
```cpp
class TreeNode {
	private:
		int element;
		TreeNode *left;
		TreeNode *right;
	public:
		// ...
}
```

### Binary Search Trees
- Each node has a key value that can be compared
- For any given node with value x,
  - Every node in the left subtree is less than x
  - Every node in the right subtree is greater than x
- Every subtree is itself a BST
- We assume we cannot have duplicate values

#### Operations on BST
**Find**: find a value *x* in the tree
Start at root
1. If x < current node, scope into left subtree.
2. If x > current node, scope into right subtree.
3. Goto 1 and continue until x is found or we "run off the tree"

```cpp
BinaryNode * BST::find(int x, BinaryNode *curNode) {
	if(curNode == NULL)
		return NULL:
	else if (x < curNode->element)
		return find(x, curNode->left);
	else if(x > curNode->element)
		return find(x, curNode->right);
	else
		return curNode;
}
```

**Note:** this can collapse into linear time if we don't balance the tree (coming soon)

**Insert**: insert a new value *x* into the tree, while keeping it as a binary search tree
```cpp
void BST::insert(int x, BinaryNode * & curNode) {
	// use a reference to a pointer, because we want our changes from 
	// exhibit A to affect the pointer of the actual tree
	if(curNode == NULL) { 
		curNode = new BinaryNode(x, NULL, NULL); // exhibit A
	} else if(x < curNode->element) {
		insert(x, curNode->left);
	} else if(x > curNode->element) {
		insert(x, curNode->right);
	}
}
```

### Remarks on time complexity of FIND, INSERT
In the worst case, an unbalanced binary tree has linear time for both FIND and INSERT
Balanced trees are logarithmic

#### More operations
`findMax`: recursively traverse down the right subtree
`findMin`: recursively traverse down the left subtree
`remove`:
  - Find node to be removed
  - Three cases:
    - Node has no children
    - Node has one child
    - Node has two children

**Remove (no children):** Just remove the node (reclaim memory), adjusting the parent's pointer to NULL

**Remove (one child):** Adjust parent's pointer to child, reclaim memory

**Remove (two children):** Replace node with successor, then remove successor from tree
  - Remove leftmost value from right subtree
    - i.e., `findMin` on right subtree

#### BST Height
an *n*-node BST has maximum depth *n*-1
- can happen if we insert data in sequence (every inserted value is a new max)

**Claim:** max nodes in a binary tree of height *h* is 2^(*h*+1) - 1

Rearranging this inequality, we have:
*h* >= log_2(*n*+1) - 1

The "shortest" tree of *n* nodes has a height of, at most, log_2(n+1) - 1 

**"Perfect" binary tree:** a binary tree where all leaves have the same depth, and all nodes have zero or two children (not one)
- has 2^h leaves
- has 2^(h+1) - 1 nodes
- Problem: a perfect binary tree can only hold *n* values where *n* = 2^(*h*+1) - 1

#### Expression trees
**Expression trees:** internal representation of a mathematical equation

Infix notation = inorder traversal of the expression tree
Postfix notation = postorder traversal of the expression tree
Prefix notation = preorder traversal of the expression tree

#### AVL Trees
*Motivation:* guarantee Theta(logn) running time on find, insert, remove
Idea: keep tree balanced after each operation
- After each insertion, rebalance if necessary
Solution: AVL trees

**Added** ***structure*** **property:**
- For every node in the tree, the height of the left and right subtrees differ by at most 1

Managed by added field: *balance factor*
- = *height of right subtree* - *height of left subtree*
- Can be computed on the fly, but that's very slow

**Balanced trees:**
- 0 means balanced
- 1 means right subtree is one longer than left
- -1 means left is one longer than right

**Unbalanced trees:**
- 2, -2 means unbalanced

- 3 or -3 are not possible because of our rebalancing property

#### AVL Trees: find, insert
**Find:** same as BST find
**Insert:** same as BST insert, but might have to "fix" tree (rebalance) after insertion

**Runtime analysis**: Theta(*d*), where *d* is the depth of the node being found/inserted
Maximum height of an *n*-node AVL tree is log_2(n + 1) - 1

###### AVL Operations
- For insert or delete, we need to "fix" balance factors
- Move back up to root, updating balance factors
  - Path from new node -> root contains the only nodes whose balance factors could change
- Do tree rotations when balance factors are 2 or -2

- We have to do, at most, two rotations

- Let *x* be the deepest node where imbalance occurs

Four cases where the insert happened:
1. In the left subtree of the left child of x
2. In the right subtree of the left child of x
3. In the left subtree of the right child of x
4. In the right subtree of the right child of x.

Cases 1 and 4: single rotation
Cases 2 and 3: double rotation

Take the given AVL tree (minuses/pluses indicate balance factor):

```
    3-
   /
  2
```
Now, we insert 1:
```
    3--
   /
  2-
 /
1
```
The insert happened in the left subtree of the left child of x, so we singly rotate right
```
  2
 / \
1   3
```
Balance!

### Side-effect of tree rotations
- At least one node moves "up"
  - Depth decreases
- At least one node moves "down"
  - Depth increases

### Right and left rotation


```
   a                  b
  / \      right     / \
  b  Z     ---->    X   a
 / \       <----       / \
X   Y       left      Y   Z
```
*a* and *b* are __pivot nodes__
**Right rotation:**
- Make a b's right child
- Replace a's left child with b's right child
**Left rotation:**
- Make b a's left child
- Replace b's right child with a's left child

Rotations preserve ordering property

### Double rotation
- Do a single rotation on the "child"
- Do a single rotation on the "parent"
