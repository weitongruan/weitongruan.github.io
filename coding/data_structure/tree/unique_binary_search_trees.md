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


