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