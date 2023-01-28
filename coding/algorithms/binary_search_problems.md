### Binary Search Problems

Binary Search problem finds a target number or a number that's relevant to that target number (for example, largest number less than that target or smallest number greater than that target, etc) within an array of ***sorted*** numbers.

Binary Search solution is able to find that number with $$O(log{n})$$ time and $$O(1)$$ space requirement. Binary Search algorithms are easy to understand, however, one needs to be extremely careful at boundries (for example, when to stop the algorithm, which direction to move when we found the target, etc).

Table of Contents:
- [How to design Binary Search Algorithms](#design)
- [Leetcode 704. Binary Search]
- [Leetcode 2529. Maximum Count of Positive Integer and Negative Ingeter]


#### How to design Binary Search Algorithms
<a name='design'></a>

The first step is to divide the search space into half and ignore the other half -- this is the step that allows us to exponentially reduce the search space (and finds the results in ***logrithmatic*** time).

As an example, if we want to find **3** from the following sorted list/array:

```
0 1 3 4 5 6 7 8 10 15 33
```




#### Number exists in sorted array, no duplicate
<a name='no_duplicate'></a>



#### Number may not exist, contains duplicates, find largest number smaller than target


#### Number may not exist, contains duplicates, find smallest number larger than target