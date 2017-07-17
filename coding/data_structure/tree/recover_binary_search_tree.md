### 99. Recover Binary Search Tree

#### General Idea:
One of the features of Binary Search Tree is that when you use in order depth first traversal, the value of each node is goint to appear in increasing(non-decreasing) order or sorted order.

For example, as in this form:

```math
1, 2, 3, 4, 5, 6, 7, 8, 9, 10
```

When two are accidentally swaped, it would result in a new list, such as:

```math
1, 2, 8, 4, 5, 6, 7, 3, 9 ,10
```

so what we can do here is to find the two wrong nodes and swap them back.

One way to find those two, is to use a pointer "**prev**" that points to the previous node and compare the value of the current node with that of the previous one. The special cases that we are looking for is:


```text
node.val < prev.val
```

For the first swaped node, the node in the wrong position  should be **prev**, while for the second, it should be the **node**.

Up to here, we almost have a general idea of what this algorithm should look like. It will perform an **in-order traversal** of the tree, which takes **O(n)** time. In terms of space, because of the recursive call, it's **O(logn)**.

***
#### Corner cases:

Need to pay attention to the cases where there are fewer than two nodes in the tree.

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