# 99 Recover Binary Search Tree

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

One way to find those two, is to use a pointer "**prev**" that points to the previous node and compare the value of the current node with that of the previous one,


```text
if node.val < prev.val, we find the node
```

For the first swaped node, it should be the **prev**, while for the second, it should be the **node**.

For corner cases,

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


