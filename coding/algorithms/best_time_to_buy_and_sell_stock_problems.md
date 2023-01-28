### Best Time to Buy and Sell Stock Problems

In this post, let's talk about a series of problems related to buy and sell stocks.


Table of Contents:
- [Leetcode 121. Best Time to Buy and Sell Stock](#121_btbss1)
- [Leetcode 122. Best Time to Buy and Sell Stock II](#122_btbss2)
- [Leetcode 123. Best Time to Buy and Sell Stock III](#123_btbss3)
- [Leetcode 188. Best Time to Buy and Sell Stock IV](#188_btbss4)
- [Leetcode 309. Best Time to Buy and Sell Stock with Cooldown](#309_btbss5)



#### Buy and sell once
<a name='121_btbss1'></a>

The base problem can be found in here:

[Leetcode 121. Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/description/)

There are two ways to solve this problem: 

The first way of thinking is to keep track of the observed minimum and update the maximum profit.

```python
class Solution(object):
    def maxProfit(self, prices):
        """
        :type prices: List[int]
        :rtype: int
        """
        if not prices:
            return 0
        
        min_price, profit = prices[0], 0
        for price in prices:
            min_price = min(price, min_price)
            profit = max(profit, price-min_price)
        
        return profit
```

The second way is to approach this problem as a ***Maximum Subarray Problem*** on an array of finite differences (prices[i] - prices[i-1]). The solution is similar to the one we discussed before in [this](https://weitongruan.github.io/coding/algorithms/continuous_sum_problems) post.

```python
class Solution(object):
    def maxProfit(self, prices):
        """
        :type prices: List[int]
        :rtype: int
        """
        if not prices:
            return 0
        
        max_profit_here, profit = 0, 0
        
        for i in range(1, len(prices)):
            max_profit_here = max(0, max_profit_here) + prices[i]-prices[i-1]
            profit = max(max_profit_here, profit)

        return profit
```

#### Buy and sell unlimited number of times
<a name='122_btbss2'></a>

The problem can be found in here:

[Leetcode 122. Best Time to Buy and Sell Stock II](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/description/)

Because there is no constraint on the number of transactions, in terms of the finite difference array, the maximum profit should correspond to the sum of positive finite differences.

```python
class Solution(object):
    def maxProfit(self, prices):
        """
        :type prices: List[int]
        :rtype: int
        """
        profit = 0
        
        for i in range(1, len(prices)):
            profit += max(0, prices[i]-prices[i-1])
            
        # sum(max(0, prices[i]-prices[i-1]) for i in range(1, len(prices)))
            
        return profit
```

#### Buy and sell at most twice

<a name='123_btbss3'></a>

The problem can be found in here:

[Leetcode 123. Best Time to Buy and Sell Stock III](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/description/)

In this problem, we are required to perform at most two transactions.

The idea is as follows: For the first transaction, it performs the same as the previous problem, but we store the maximum profit (`max_profit_here[i] = max(max_profit_here[i-1], profit_if_sell_here)`) at each time index. For the second transaction, a `temp_max` keeps track of the maximum `max_profit_here` from previous transaction minus the `prices[i]`. This `temp_max` stores from current time index `i`, what's the best time to start the second transaction. 


mph[kk][i] = max(mph[kk][i-1], max_j(mph[kk-1][j] + prices[i] - prices[j]))

= max(mph[kk][j-1], prices[i] + max_j(mph[kk-1][j] - prices[j]))

```python
class Solution(object):
    def maxProfit(self, prices):
        """
        :type prices: List[int]
        :rtype: int
        """
        if not len(prices):
            return 0
        
        n = len(prices)
        k = 2
        mph = [[0]*n for _ in range(k+1)] # max_profit_here
        # Notice the first row of mph is all zeroes
        # which makes the first iteration of i the same as problem 1
        
        for i in range(1, k+1):
            temp_max = mph[i-1][0] - prices[0]
            for j in range(1, n):
                mph[i][j] = max(mph[i][j-1], prices[j] + temp_max)
                temp_max = max(temp_max, mph[i-1][j]-prices[j])
                
        return mph[-1][-1]
```

```python
class Solution(object):
    def maxProfit(self, prices):
        """
        :type prices: List[int]
        :rtype: int
        """
        if not len(prices):
            return 0
        
        n = len(prices)
        k = 2
        mph = [[0]*n for _ in range(2)] # max_profit_here
        # Reduce space complexity from O(k*n) to O(n)
        
        for i in range(k):
            temp_max = mph[i%2][0] - prices[0]
            for j in range(1, n):
                mph[(i+1)%2][j] = max(mph[(i+1)%2][j-1], prices[j] + temp_max)
                temp_max = max(temp_max, mph[i%2][j]-prices[j])
                
        return mph[(i+1)%2][-1]
        
```

#### Buy and sell at most k times

<a name='188_btbss4'></a>

The problem can be found in here:

[Leetcode 188. Best Time to Buy and Sell Stock IV](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iv/description/)

```python
class Solution(object):
    def maxProfit(self, k, prices):
        """
        :type k: int
        :type prices: List[int]
        :rtype: int
        """
        if not prices:
            return 0
            
        n = len(prices)
        mph = [0]*n # max_profit_here, only n extra space.
        
        # If we k is large, then it's the same as unlimited transactions.
        if k >= n//2:
            return sum(max(0, price[i]-prices[i-1]) for i in range(1, n))
        
        for i in range(k):
            temp_max = mph[0] - prices[0]
            for j in range(1, n):
                mph[j], temp_max = max(mph[j-1], mph[j]+temp_max), max(temp_max, mph[j]-prices[j])
        
        return mph[-1]
        
```

#### Buy and sell unlimited times but with cooldown

<a name='309_btbss5'></a>

The problem can be found in here:

[Leetcode 309. Best Time to Buy and Sell Stock with Cooldown](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/description/)


profit[n] = max(profit[n-1], max_i(prices[n] - prices[i]+profit[i-2]))

= max(profit[n-1], prices[n] + max_i(profit[i-2] - prices[i]))



```python
class Solution(object):
    def maxProfit(self, prices):
        """
        :type prices: List[int]
        :rtype: int
        """
        if len(prices) < 1:
            return 0
        
        n = len(prices)
        profit = [0]*(n+2)
        max_temp = profit[0]-prices[0]
        
        for i in range(n):
            profit[i+2] = max(profit[i+1], prices[i]+max_temp)
            max_temp = max(max_temp, profit[i]-prices[i])
        
        return profit[-1]
```

