### Continuous Sum Equals K Problems

In this post, let's talk about a series of problems where they require a continuous sum equals K. The sum can be over an array, a linked list or a tree.

***Key ideas:***

***Use a dictionary to store the number of each cumulative sums at each index. For each number, simply update the total nunber by dic[sum_here-K].***

`cont_sum = sum_here - cumulative_sum = K`


Table of Contents:
- [Leetcode 560. Subarray Sum Equals K](#560_sseK)
- [Leetcode 437. Path Sum III](#437_ps3)

#### On an array

<a name='560_sseK'></a>

[Leetcode 560. Subarray Sum Equals K](https://leetcode.com/problems/subarray-sum-equals-k/description/)

```python
class Solution(object):
    def subarraySum(self, nums, k):
        """
        :type nums: List[int]
        :type k: int
        :rtype: int
        """
        
        # build table on the go
        sum_so_far = 0
        table = {0: 1}
        res = 0
        
        for num in nums:
            sum_so_far += num
            res += table.get(sum_so_far-k, 0)
            table[sum_so_far] = table.setdefault(sum_so_far, 0) + 1
                  
        return res
```


#### On a tree

<a name='437_ps3'></a>
[Leetcode 437. Path Sum III](https://leetcode.com/problems/path-sum-iii/description/)

O(n) algorithm, hash table to save a variable *sum_to_now-target*. 

***Need to remember getting rid of each inserted element so that it won't impact other paths***

```python
class Solution(object):
    def pathSum(self, root, sum):
        """
        :type root: TreeNode
        :type sum: int
        :rtype: int
        """
        res = [0]
        
        def dfs(root, cumul_sum, table_prev_sum):
            if not root:
                return
            
            cumul_sum += root.val
            res[0] += table_prev_sum.get(cumul_sum-sum, 0)
            table_prev_sum[cumul_sum] = table_prev_sum.setdefault(cumul_sum, 0) + 1
            
            dfs(root.left, cumul_sum, table_prev_sum)
            dfs(root.right, cumul_sum, table_prev_sum)
            # Don't forget to delete this entry from dictionary, so that it
            # won't influence other paths
            table_prev_sum[cumul_sum] -= 1
            
        dfs(root, 0, {0: 1})
        
        return res[0]
```

