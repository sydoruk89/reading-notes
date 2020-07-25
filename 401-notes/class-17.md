# Trees
## Common Terminology
- Node - A node is the individual item/data that makes up the data structure
- Root - The root is the first/top Node in the tree
- Left Child - The node that is positioned to the left of a root or node
- Right Child - The node that is positioned to the right of a root or node
- Edge - The edge in a tree is the link between a parent and child node
- Leaf - A leaf is a node that does not contain any children
- Height - The height of a tree is determined by the number of edges from the root to the bottommost node

## Traversals
An important aspect of trees is how to traverse them. Traversing a tree allows us to search for a node, print out the contents of a tree, and much more! There are two categories of traversals when it comes to trees:
- Depth First
- Breadth First

## Depth First
- Pre-order: root >> left >> right (A, B, D, E, C, F)
- In-order: left >> root >> right (D, B, E, A, F, C)
- Post-order: left >> right >> root (D, E, B, F, C, A) \

The most common way to traverse through a tree is to use **recursion**. With these traversals, we rely on the call stack to navigate back up the tree when we have reached the end of a sub-path.

### Pre-order

ALGORITHM preOrder(root)

  OUTPUT <-- root.value

  if root.left is not NULL
      preOrder(root.left)

  if root.right is not NULL
      preOrder(root.right)

## Traversal Pseudocode

Pre-order \
ALGORITHM preOrder(root) \
// INPUT <-- root node \
// OUTPUT <-- pre-order output of tree node's values

    OUTPUT <-- root.value

    if root.left is not Null
        preOrder(root.left)

    if root.right is not NULL
        preOrder(root.right)
In-order \
ALGORITHM inOrder(root) \
// INPUT <-- root node \
// OUTPUT <-- in-order output of tree node's values

    if root.left is not NULL
        inOrder(root.left)

    OUTPUT <-- root.value

    if root.right is not NULL
        inOrder(root.right)
Post-order \
ALGORITHM postOrder(root) \
// INPUT <-- root node \
// OUTPUT <-- post-order output of tree node's values

    if root.left is not NULL
        postOrder(root.left)

    if root.right is not NULL
        postOrder(root.right)

    OUTPUT <-- root.value

## Breadth First
Breadth first traversal iterates through the tree by going through each level of the tree node-by-node. Output: A, B, C, D, E, F. 
Traditionally, breadth first traversal uses a queue (instead of the call stack via recursion) to traverse the width/breadth of the tree. 

1. From our dequeued node A, we can enqueue the left and right child (in that order).
2. This leaves us with B as the new front of our queue. We can then repeat the process we did with A: Dequeue the front node, enqueue that node’s left and right nodes, and move to the next new front of the queue.
3. Now our front is C, so we repeat the dequeue + enqueue children process.
4. And we continue onwards. When we reach a node that doesn’t have any children, we just dequeue it without any further enqueue.

ALGORITHM breadthFirst(root) \
// INPUT  <-- root node \
// OUTPUT <-- front node of queue to console

  Queue breadth <-- new Queue() \
  breadth.enqueue(root)

  while breadth.peek() \
    node front = breadth.dequeue() \
    OUTPUT <-- front.value

    if front.left is not NULL
      breadth.enqueue(front.left)

    if front.right is not NULL
      breadth.enqueue(front.right)

## Binary Trees
### Big O
The Big O time complexity for inserting a new node is O(n). Searching for a specific node will also be O(n). Because of the lack of organizational structure in a Binary Tree, the worst case for most operations will involve traversing the entire tree. If we assume that a tree has n nodes, then in the worst case we will have to look at n items, hence the O(n) complexity.

The Big O space complexity for a node insertion using breadth first insertion will be O(w), where w is the largest width of the tree. For example, in the above tree, w is 4.

A “perfect” binary tree is one where every non-leaf node has exactly two children. The maximum width for a perfect binary tree, is 2^(h-1), where h is the height of the tree. Height can be calculated as log n, where n is the number of nodes.

## Searching a BST
Searching a BST can be done quickly, because all you do is compare the node you are searching for against the root of the tree or sub-tree. If the value is smaller, you only traverse the left side. If the value is larger, you only traverse the right side.

Let’s say we are searching 15. We start by comparing the value 15 to the value of the root, 23.

15 < 23, so we traverse the left side of the tree. We then treat 8 as our new “root” to compare against.

15 > 8, so we traverse the right side. 16 is our new root.

15 < 16, so we traverse the left side. And aha! 15 is our new root and also a match with what we were searching for.

The best way to approach a BST search is with a **while loop**. We cycle through the while loop until we hit a leaf, or until we reach a match with what we’re searching for.

## Big O
The Big O time complexity of a Binary Search Tree’s insertion and search operations is O(h), or O(height). In the worst case, we will have to search all the way down to a leaf, which will require searching through as many nodes as the tree is tall. In a balanced (or “perfect”) tree, the height of the tree is log(n). In an unbalanced tree, the worst case height of the tree is n.

The Big O space complexity of a BST search would be O(1). During a search, we are not allocating any additional space.