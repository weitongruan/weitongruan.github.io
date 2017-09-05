### Continuous Sum Problems

In this post, let's focus on a set of problems which requires a maximum sum across a continuous region (subarray in arrays or path in trees). The same methodology can be applied to arrays, linked-lists and (binary) trees.

Let's first take a look at one example on arrays. 

#### Arrays and Linked-lists

[Leetcode 53. Maximum Subarray](https://leetcode.com/problems/maximum-subarray/description/)

To solve this problem, all we neet to do is to iterate through the given array, keep track of two variables (*max_end_here* and *max_overall*) and update them when necessary.

The general idea is, the array that generates the largest sum has to end at some element in the array. For every element in the array, we first calculate the maximum sum of the array ending in here and then update the overall maximum.

The code is as follows:

```python
class Solution(object):
    def maxSubArray(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        if not nums:
            return 0
        
        # Initialize two variables
        max_end_here, max_overall = 0, nums[0]
        
        for num in nums:
            # To decide if previous sum is needed at current index
            max_end_here = max(0, max_end_here) + num
            # Update global sum
            max_overall = max(max_overall, max_end_here)
        
        return max_overall
```

Apparently, the ***time complexity*** is ***O(n)*** and ***space complexity*** is ***O(1)***.

Also apparently, this algorithm can be used on a ***linked-list*** directly.

#### What if we use a tree?

[Leetcode 124 Binary Tree Maximum Path Sum](https://leetcode.com/problems/binary-tree-maximum-path-sum/description/)

The problem is the same, we need to find the maximum value over a continuous region, which is a path in a tree.

The answer is ***Use the same methodology above with a recursive setting***.

We still want to go over each node in the tree and update a global maximum variable. A slight change here is for each node, we first need to calculate *maxEndHere(node.left)* and *maxEndHere(node.right)*, and then update the global maximum value. (Note, compared with the previous example on an array, here the global maximum is `max(global_max, l_max + r_max + root.val)`.)

***A poster-order recursive traversal is also used.***


```python
# Definition for a binary tree node.
# class TreeNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution(object):
    def maxPathSum(self, root):
        """
        :type root: TreeNode
        :rtype: int
        """
        res = [-float('inf')]
        
        def maxEndHere(root):
            if not root:
                return -float('inf')
            
            l = max(0, maxEndHere(root.left))
            r = max(0, maxEndHere(root.right))
            res[0] = max(res[0], l+r+root.val)
                
            return max(l, r) + root.val
        
        maxEndHere(root)
        
        return res[0]
```
Time complexity is ***O(n)***.


