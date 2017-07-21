
## Table of Problems

[95 Unique Binary Search Trees II](#unique_binary_search_trees_2)

[96 Unique Binary Search Trees I](#unique_binary_search_trees)

[98 Validate Binary Search Tree](#validate_binary_search_tree)

[99 Recover Binary Search Tree](#recover_binary_search_tree)

[99 Revover Binary Search Tree with Morris Traversal](#recover_binary_search_tree_with_morris_traversal)

---
---
<a name='unique_binary_search_trees_2'></a>
### 95 Unique Binary Search Trees II

The problem can be found in [here](https://leetcode.com/problems/unique-binary-search-trees-ii/#/description).

Compared with [#96](#unique_binary_search_trees), the problem requires the complete set of unique BSTs.

#### General Idea:

Divide and Conquer:

suppose we have a function **generate(i, j)** which takes in a start **i** and a end **j** **(i <= j)** and generate a list of roots each corresponds to a unique BST.

Then our result **generateTrees(n)** is simply **generate(1, n)**.

To implement **generate(i,j)**, all we need to do is to use an iterator **k** loops from **i** to **j**, each time selecting **k** as the root build trees such that root.left is from **generate(i, k-1)** and root.right is from **generate(k, j)**.

---
##### Codes

```python
# Definition for a binary tree node.
# class TreeNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None
```

```python
class Solution(object):
    def generateTrees(self, n):
        """
        :type n: int
        :rtype: List[TreeNode]
        """
        if not n:
            return []
        else:
            return self.generate(1, n)
    
    def generate(self, i, j):
        ret = []
        if i > j:
            ret.append(None)
        else:
            for k in range(i, j+1):
                left = self.generate(i, k-1)
                right = self.generate(k+1, j)
                for subtreeleft in left:
                    for subtreeright in right:
                        root = TreeNode(k)
                        root.left = subtreeleft
                        root.right = subtreeright
                        ret.append(root)
        return ret
```

---
---
<a name='unique_binary_search_trees'></a>
### 96 Unique Binary Search Trees

#### General idea:

Let's first take a look at a few examples:

- n = 1: definitely return 1

- n = 2: we have 2 nodes (1 or 2) that can be placed as root, once we pick one as root, the other either becomes its left child or right child, and the other child is **None**. This gives us: 

$$ 1*1 + 1*1 = 2 $$

- n = 3: again, we have 3 nodes that can be chosen as root:
    * If we pick 1 as root, we leave **None** as its **left** child and $$\{2, 3\}$$ as its **right** subtree, from the previous case where n = 2, we know we can have 2 unique subtree, so in this case, the unique number of BSTs is:
    $$ 1*2 = 2 $$
    
    * If we pick 2 as root, we leave $$1$$ as **left** subtree and $$3$$ as **right** subtree, it's obvious that here the unique number of BSTs is:
    $$ 1*1 = 1 $$
    
    * If we pick 3 as root, this case will be just the same as if we pick 1, because of symmetry. Again, in this case, we have 2 more unique BSTs:
    $$ 2*1 = 1 $$
    In this case, we should return:
    
$$ 1*2 + 1*1 + 2*1 = 5 $$

**We notice here that: first, due to the BST condition, the ordering doesn't matter, only the number of nodes in a tree matters; second, the result is a summation of possible unique BSTs from $$n$$ cases, where for each case, the possible unique BSTs is a product of that of the left subtree and that of the right subtree**

---
#### Codes

Now, we almost have the algorithm, if written in a recursive way, it looks like:

```python
def numTrees(n):
    # base case
    if n == 0 : return 1
    
    res = 0
    for i in range(n):
        res += numTrees(i) * numTrees(n-1-i)
        
    return res
```

This recursive algorithm falls in the very common case where it can be improved by dynamic programming due to tons of repeated calculations.

```python
def numTrees(n):
    dp = [1]
    for i in range(1, n+1):
        dp.append(sum([dp[j]*dp[i-1-j] for j in range(i)]))
    
    return dp[-1]
```

One simple improvements we can make it to take advantage of the symmetry property, i.e. instead of letting the second loop to go from $$0$$ to $$i$$, let it stop at $$i//2$$, double the sum and plus an extra term (dp[ $$i//2$$ ]) if $$i$$ is an odd number. However, I'm not sure if this would speed it up, I guess it helps when n is large.

```python
    def numTrees(n):
    dp = [1]
    for i in range(1, n+1):
        dp.append(2*sum(dp[j]*dp[i-1-j] for j in range(i//2+1)))
        if n % 2:
            dp[-1] -= dp[i//2]**2
        # if n % 2:
        #     dp.append(2*sum([dp[j]*dp[i-1-j] for j in range(i//2)])+dp[i//2]**2)
        # else:
        #     dp.append(2*sum(dp[j]*dp[i-1-j] for j in range(i//2+1)))
```

---
#### Take aways:

When the keys to a hash table (dictionary) is integers (0-N), we can simply use a array(list) to achieve the same functionality.

---
---
<a name='validate_binary_search_tree'></a>
### 98 Validate Binary Search Tree

#### General Idea:

To validate if a given tree is BST, we need to stick to its definition.

For a BST:

* value of the root should be larger than the maximum from its left subtree
* value of the root should be smaller than the minimum from its right subtree
* both left and right subtrees should also be BSTs

The first algorithm we can try is to do in-order traversal and make sure the value of each node we output is increasing.

The second is to use a top down checking algorithm, checking if the tree satifies the BST condition level by level.

---
#### Codes

```python
# Definition for a binary tree node.
# class TreeNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None
```
A recursive in-order traversal algorithm:

```python
class Solution(object):
    def isValidBST(self, root):
        """
        :type root: TreeNode
        :rtype: bool
        """
        prev = [TreeNode(-float('inf'))]
        res = [True]
        def dfs_inorder(node):
            if not node: return
            dfs_inorder(node.left)
            if node.val <= prev[0].val:
                res[0] = False
            prev[0] = node
            dfs_inorder(node.right)
        
        dfs_inorder(root)
        return res[0]
```
A recursive algorithm

```python
class Solution(object):
    def isValidBST(self, root):
        """
        :type root: TreeNode
        :rtype: bool
        """
        def helper(node, minNode, maxNode):
            if not node:
                return True
            # node level has to satisfy these conditions    
            if (minNode and node.val <= minNode.val) or \
               (maxNode and node.val >= maxNode.val):
                return False
            # both left and right subtree also has to be BST
            return helper(node.left, minValue, node) and \
                   helper(node.right, node, maxValue)
                   
        return helper(root, None, None)
```

---
---
<a name='recover_binary_search_tree'></a>
### 99. Recover Binary Search Tree

#### General Idea:
One of the features of Binary Search Tree is that when you use in order depth first traversal, the value of each node is goint to appear in increasing(non-decreasing) order or sorted order.

For example, as in this form:

```math
1, 2, 3, 4, 5, 6, 7, 8, 9, 10
```

When two are accidentally swapped, it would result in a new list, such as:

```math
1, 2, 8, 4, 5, 6, 7, 3, 9 ,10
```

so what we can do here is to find the two wrong nodes and swap them back.

One way to find those two, is to use a pointer "**prev**" that points to the previous node and compare the value of the current node with that of the previous one. The special cases that we are looking for is:


```text
node.val < prev.val
```

For the first swapped node, the node in the wrong position  should be **prev**, while for the second, it should be the **node**.

Up to here, we almost have a general idea of what this algorithm should look like. It will perform an **in-order traversal** of the tree, which takes **O(n)** time. In terms of space, because of the recursive call, it’s **O(n)**.

***
#### Corner cases:

Need to pay attention to the case where the swapped nodes are next to each other.

***
#### Codes:

Basics of TreeNode class:
```python

# Definition for a binary tree node.
# class TreeNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

```

My first iterative algorithm looks like:

```python

class Solution(object):
    def recoverTree(self, root):
        """
        :type root: TreeNode
        :rtype: void Do not return anything, modify root in-place instead.
        """
        if not root.left and not root.right:
            return root
        
        def dfs_inorder(node):
            wrong = []
            stack = []
            prev = TreeNode(-float('inf'))
            flag = False
            while stack or node:
                if node:
                    stack.append(node)
                    node = node.left
                else:
                    node = stack.pop()
                    if not flag and node.val < prev.val:
                        wrong.append(prev)
                        wrong.append(node)
                        flag = True
                    elif flag and node.val < prev.val:
                        wrong.pop()
                        wrong.append(node)
                        return wrong
                    prev = node
                    node = node.right
            return wrong
                    
        wrong_nodes = dfs_inorder(root)
        wrong_nodes[0].val, wrong_nodes[1].val = wrong_nodes[1].val, wrong_nodes[0].val
        
```

It can be easily improved to this, getting rid of the flag:

```python

class Solution(object):
    def recoverTree(self, root):
        """
        :type root: TreeNode
        :rtype: void Do not return anything, modify root in-place instead.
        """
        
        def dfs_inorder(node):
            first, second = None, None
            stack = []
            prev = TreeNode(-int('inf'))
            while stack or node:
                if node:
                    stack.append(node)
                    node = node.left
                else:
                    node = stack.pop()
                    if not first and node.val < prev.val:
                        first, second = prev, node
                    elif first and node.val < prev.val:
                        second = node
                        return (first, second)
                    prev, node = node, node.right
            return (first, second)
                    
        wrong_nodes = dfs_inorder(root)
        wrong_nodes[0].val, wrong_nodes[1].val = wrong_nodes[1].val, wrong_nodes[0].val
        
```

For a recursive algorithm:

(Note: we make first and second and pre a list is to make them mutable)

```python

class Solution(object):
    def recoverTree(self, root):
        """
        :type root: TreeNode
        :rtype: void Do not return anything, modify root in-place instead.
        """
        
        first, second = [], [None]
        pre = [TreeNode(-float('inf'))]
        
        def dfs_inorder(node):

            if not node:
                return
            
            dfs_inorder(node.left)
            
            if not len(first) and node.val < pre[0].val:
                first.append(pre[0])
            if len(first) and node.val < pre[0].val:
                second[0] = node
            
            pre[0] = node
                
            dfs_inorder(node.right)
            return
              
        dfs_inorder(root)
        first[0].val, second[0].val = second[0].val, first[0].val
```

---
---
<a name='recover_binary_search_tree_with_morris_traversal'></a>
### 99 Recover Binary Search Tree with Morris Traversal:

#### Background on Morris Traversal
A good tutorial on Morris Traversal can be found 
[here](http://www.cnblogs.com/AnnieKim/archive/2013/06/15/morristraversal.html).


---
#### Codes

```python

class Solution(object):
    def recoverTree(self, root):
        """
        :type root: TreeNode
        :rtype: void Do not return anything, modify root in-place instead.
        """
        first, second = None, None
        previous = TreeNode(-float('inf'))
        curr, pre = root, None
        
        while curr:
            if not curr.left:
                # print(curr.val)
                if previous and previous.val > curr.val:
                    if not first:
                        first, second = previous, curr
                    else:
                        second = curr
                previous = curr
                curr = curr.right
            
            else:
                pre = curr.left
                while pre.right and pre.right != curr:
                    pre = pre.right

                if not pre.right:
                    pre.right = curr
                    curr = curr.left

                else:
                    pre.right = None
                    # print(curr.val)
                    if previous and previous.val > curr.val:
                        if not first:
                            first, second = previous, curr
                        else:
                            second = curr
                    previous = curr
                    curr = curr.right

        first.val, second.val = second.val, first.val
```
