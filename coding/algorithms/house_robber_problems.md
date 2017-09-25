### House Robber Problems

In this post, let's look at the series of house robber problems, where a robber or a group of them tries to break into houses and rob money. The constraints that they are facing is that no two consecutive houses can be robbed.

This series of problems can be solved via DP.

Table of Contents:
- [On a list(array)](#list)
- [On a binary tree](#binary_tree)
- [On a circle](#circle)


<a name='list'></a>
#### On a list

[Leetcode 198. House Robber](https://leetcode.com/problems/house-robber/description/)

Suppose the money in each house is stored in a list.

Then the update formula:
$$
s(i) = \max(s(i-1), s(i-2)+v_i)
$$
where $$s(i)$$ stands for the max money they can rob at the i-th house.

```python
class Solution(object):
    def rob(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        prev, now = 0, 0
        
        for num in nums:
            prev, now = now, max(prev+ num, now)
        
        return now
```

<a name='binary_tree'></a>
#### On a binary tree

[Leetcode 337 House Robber III](https://leetcode.com/problems/house-robber-iii/description/)

The update formula is:
$$
\begin{aligned}
s(node, 0) &= s(node.left, 1)+s(node.right, 1)+node.val \\
s(node, 1 ) &= \max(s(node.left, 0), s(node.left, 1)) + \max(s(node.right, 0), s(node.right, 1))
\end{aligned}
$$
where $$s(node, 0)$$ stands for the maximum profit at node and node is used, whereas $$s(node, 1)$$ stands for the max profit where node is not used. At each node, we update both two values.


The difficult about dealing with a binary tree is that we don't always have access to the grandchildren of the node.

```python
# Definition for a binary tree node.
# class TreeNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution(object):
    def rob(self, root):
        """
        :type root: TreeNode
        :rtype: int
        """
        if not root: return 0
        
        def helper(node):
            if not node: return 0,0
            
            left = helper(node.left)
            right = helper(node.right)
            return left[1]+right[1]+node.val, max(left[0], left[1])+ max(right[0], right[1])
        
        res = helper(root)
        return max(res[0], res[1])
```







