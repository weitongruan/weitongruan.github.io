### Sum Problems

Sum problem is a class of problems where the goal is to find a list of elements such that they sum to a target. There are lots of similar problems in Leetcode and they can take form in lists and trees.


#### List(array):

##### Not sorted: 
- Use a pointer to move from left to right with a dictionary(hash table) to store visited numbers. 
- Time: O(n). Space: O(n)
- List of problems:
    - [1. Two Sum](https://leetcode.com/problems/two-sum/description/): Solution is [here](#1_two_sum).

   
##### Sorted: 
- Two pointers, move from both ends to the middle. 
- Time: O(n). Space: O(1)
- Can easily get rid of duplicates during iteration
- List of problems:
    - [15. 3Sum](https://leetcode.com/problems/3sum/description/): Solution is [here](#15_3sum)
    - [16. 3Sum Closest](https://leetcode.com/problems/3sum-closest/description/): Solution is [here](#16_3sum_closest)
    - [259. 3Sum Smaller](https://leetcode.com/problems/3sum-smaller/description/): Solution is [here](#259_3sum_smaller)
    - [611. Valid Triangle Number](https://leetcode.com/problems/valid-triangle-number/description/): Solution is [here](#611_valid_triangle_number)
    - [18. 4Sum](https://leetcode.com/problems/4sum/description/): Solution is [here](#18_4sum)
    - [39. Combination Sum](https://leetcode.com/problems/combination-sum/description/): Solution is [here](#39_combination_sum)
    - [40. Combination Sum 2](https://leetcode.com/problems/combination-sum-ii/description/): Solution is [here](#40_combination_sum_2)




#### Solutions:

<a name='15_3sum'></a>
##### 15. 3Sum


No duplicate in result.

This is the base case for all larger sum problems

```python
class Solution(object):
    def threeSum(self, nums):
        """
        :type nums: List[int]
        :rtype: List[List[int]]
        """
        
        res = []
        nums.sort()
        
        for idx in range(len(nums)-2):
            if nums[idx] > 0: break
            if idx > 0 and nums[idx] == nums[idx-1]: continue
            
            l, r = idx+1, len(nums)-1
            while l < r:
                s = nums[l] + nums[r]
                
                if not (s+nums[idx]):
                    res.append([nums[idx], nums[l], nums[r]])
                    l += 1
                    # get rid of duplicates
                    while l < r and nums[l] == nums[l-1]:
                        l += 1
                
                elif (s+nums[idx]) > 0:
                    r -= 1
                else:
                    l += 1
        
        return res
                    
```


<a name='16_3sum_closest'></a>
##### 16. 3Sum Closest


```python
class Solution(object):
    def threeSumClosest(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: int
        """
        if len(nums) == 3: return sum(nums)
        nums.sort()
        if target < sum(nums[:3]): return sum(nums[:3])
        elif target > sum(nums[-3:]): return sum(nums[-3:])
        
        result, diff = 0, sys.maxint
        for i in range(len(nums)-2):
            if i > 0 and nums[i] == nums[i-1]: continue
            
            start, end = i+1, len(nums)-1
            while start < end:
                total = nums[i] + nums[start] + nums[end]
                temp_diff = abs(total-target)
                
                if not temp_diff: return target
                elif temp_diff < diff: result, diff = total, temp_diff
                if total < target: start += 1
                elif total > target: end -= 1
        
        return result 
```



<a name='259_3sum_smaller'></a>
##### 259. 3Sum smaller

The algorithm is almost the same as [611. Valid Triangle Number](#611_valid_triangle_number), except in this problem we're counting the two sum that is smaller than a target, while in that problem, we count the sum that is larger than a target.

```python
class Solution(object):
    def threeSumSmaller(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: int
        """
        count = 0
        nums.sort()
        
        for k in range(len(target)-2):
            i, j = k+1, len(target)-1
            if nums[k] + nums[i] + nums[j] < target:
                count += j-i
                i += 1
            else:
                j -= 1
        
        return count
    

```




<a name='611_valid_triangle_number'></a>
##### 611. Valid Triangle Number

Normally, this is a $$O(n^3)$$ algorithm, since we have to pick three elements from the given list and the total number of combinations is $$C^n_3$$.

Instead of using ***backtracking***, here we can design a $$O(n^2)$$ algorithm and it is similar to the two pointer approach that we use to solve two sum or three sum problems.

The code with comments is provided below:

```python
class Solution(object):
    def triangleNumber(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """

        res = 0
        if len(nums) < 3: return res

        nums.sort()  # Sort makes it easy to use two pointers
        
        start = 0  # Get rid of leading zeros
        while start < len(nums) and not nums[start]:
            start += 1
        
        # The idea here is similar to three sum, where we use a two
        # pointer approach to avoid unnecessary comparisons.
        # Note: pay attention to how the counts are updated!!
        for i in range(start, len(nums))[::-1]:
            l, r = start, i-1
            
            while l < r:
                if nums[l] + nums[r] > nums[i]:
                    res += r-l
                    r -= 1
                else:
                    l += 1
                
        return res

```


<a name='18_4sum'></a>
##### 18. 4Sum


Extended to N sum

```python
class Solution(object):
    def fourSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[List[int]]
        """
        res = []
        nums.sort()
        
        def findNsum(N, target, start_idx, prev):
            if len(nums) - start_idx < N or N < 2 or \
                target > nums[-1]*N or target < nums[start_idx]*N:
                return
            if N == 2:
                l, r = start_idx, len(nums)-1
                while l < r:
                    s = nums[l] + nums[r]
                    if s == target:
                        res.append(prev+[nums[l], nums[r]])
                        l += 1
                        
                        while l < r and nums[l] == nums[l-1]:
                            l += 1
                        
                    elif s > target:
                        r -= 1
                    else:
                        l += 1
            
            else:
                for idx in range(start_idx, len(nums)-N+1):
                    if idx > start_idx and nums[idx] == nums[idx-1]:
                        continue
                    else:
                        findNsum(N-1, target-nums[idx], idx+1, prev+[nums[idx]])
        
        findNsum(4, target, 0, [])
        
        return res

```


<a name='39_combination_sum'></a>
##### 39. Combination Sum

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



<a name='40_combination_sum_2'></a>
##### 40. Combination Sum 2


Duplicates exist in the input array. Each element can be used only once.

No duplicate in the result.

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

