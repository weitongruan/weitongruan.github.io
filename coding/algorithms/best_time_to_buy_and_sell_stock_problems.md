### Best Time to Buy and Sell Stock Problems

In this post, let's talk about a series of problems related to buy and sell stocks.


Table of Contests:
- [Leetcode 121. Best Time to Buy and Sell Stock](#121_btbss1)
- [Leetcode 122. Best Time to Buy and Sell Stock II](#122_btbss2)
- [Leetcode 123. Best Time to Buy and Sell Stock III](#123_btbss3)



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

#### Buy and sell at most k times

<a name='123_btbss3'></a>

The problem can be found in here:

[Leetcode 123. Best Time to Buy and Sell Stock III](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/description/)



