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
