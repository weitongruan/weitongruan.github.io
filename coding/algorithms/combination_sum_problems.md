### Combination Sum Problems
In this post, we look at the combination sum problems.

***Key ideas:***

***For most of the combination problems, backtracking is an efficient way.***

Table of Contents:
- [Leetcode 39. Combination Sum](#39_cs)
- [Leetcode 40. Combination SumII](#40_cs2)
- [Leetcode 216. Combination Sum III](#216_cs3)
- [Leetcode 337. Combination Sum IV](#337_cs4)



#### No duplicate element, unlimited usage
<a name='39_cs'></a>

[Leetcode 39. Combination Sum](https://leetcode.com/problems/combination-sum/description/)

No duplicate in the input array, each element can appear unlimited number of times.

No duplicate in the result.


```python

class Solution(object):
    def combinationSum(self, candidates, target):
        """
        :type candidates: List[int]
        :type target: int
        :rtype: List[List[int]]
        """
        res = []
        candidates.sort()
        
        def dfs(index, target, prev):
            if not target:
                res.append(prev)
                return
                
            cter = index
            while cter < len(candidates) and candidates[cter] <= target:
                dfs(cter, target-candidates[cter], prev+[candidates[cter]])
                
                cter += 1
        
        dfs(0, target, [])
        
        return res
            
```

<a name='40_cs2'></a>
#### With duplicate elements, one use per element

[Leetcode 40. Combination Sum II](https://leetcode.com/problems/combination-sum-ii/description/)

Duplicates exist in the input array. Each element can be used only once.

No duplicate in the result.

***Note: This algorithm provides a great way to deal with duplicate elements in backtracking.***

```python
class Solution(object):
    def combinationSum2(self, candidates, target):
        """
        :type candidates: List[int]
        :type target: int
        :rtype: List[List[int]]
        """
        
        res = []
        candidates.sort()
        
        def dfs(index, target, prev):
            if not target:
                res.append(prev)
                return
            
            cter = index
            while cter < len(candidates) and candidates[cter] <= target:
                # deal with duplicate, always consider the first one
                # and ignore other duplicate elements.
                if cter-1 >= index and candidates[cter-1] == candidates[cter]:
                    cter += 1
                else:  
                    dfs(cter+1, target-candidates[cter], prev+[candidates[cter]])
            
                    cter += 1
                
        dfs(0, target, [])
        
        return res
                    
```

<a name='216_cs3'></a>
#### length is fixed

[Leetcode 216. Combination Sum III](https://leetcode.com/problems/combination-sum-iii/description/)

```python
class Solution(object):
    def combinationSum3(self, k, n):
        """
        :type k: int
        :type n: int
        :rtype: List[List[int]]
        """
        
        res = []
        
        def dfs(counter, total, prev):
            if counter == k:
                if total == n:
                    res.append(prev)
                return
            
            start = prev[-1]+1 if counter > 0 else 1
            for i in range(start, min(9,n-total)+1):
                dfs(counter+1, total+i, prev+[i])
                
        dfs(0, 0, [])
        
        return res
```


<a name='377_cs4'></a>
#### Only the number of possible combination is needed

[Leetcode 377. Combination Sum IV](https://leetcode.com/problems/combination-sum-iv/description/)

```python
class Solution(object):
    def combinationSum4(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: int
        """
        
        res = [1] + [0]*target
        nums.sort()
        
        for i in range(1, target+1):
            for num in nums:
                if num > i: break
                res[i] += res[i-num]
        
        return res[target]
        
```


