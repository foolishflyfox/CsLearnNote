---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 122. Best Time to Buy and Sell Stock II

> <https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/description/>

## Description

Say you have an array for which the <em>i<sup> th</sup></em> element is the price of a given stock on day *i*.

Design an algorithm to find the maximum profit. You may complete as many transactions as you like (i.e., buy one and sell one share of the stock multiple times).

**Note**: You may not engage in multiple transactions at the same time (i.e., you must sell the stock before you buy again).


**Example 1**:
**Input**: `[7,1,5,3,6,4]`
**Output**: 7
**Explanation**: Buy on day 2 (price = 1) and sell on day 3 (price = 5), profit = 5-1 = 4.
Then buy on day 4 (price = 3) and sell on day 5 (price = 6), profit = 6-3 = 3.

**Example 2**:
**Input**: `[1,2,3,4,5]`
**Output**: 4
**Explanation**: Buy on day 1 (price = 1) and sell on day 5 (price = 5), profit = 5-1 = 4.
Note that you cannot buy on day 1, buy on day 2 and sell them later, as you are engaging multiple transactions at the same time. You must sell before buying again.

**Example 3**:
**Input**: `[7,6,4,3,1]`
**Output**: 0
**Explanation**: In this case, no transaction is done, i.e. max profit = 0.

## Solution

### Solution-1

使用递归动态规划：要求所有 n 个数的最大利润，可以有公式(设求利润的函数为 $f(prices)$ )：
$$
\begin {aligned}
f(prices)=max\{ & f(prices[1:]), \\
& -prices[0]+price[1]+f(prices[2:]), \\
& -prices[0]+price[2]+f(prices[3:]), \\
& \cdots, \\
& -prices[0]+price[n-1]+f(prices[n:]) \\
& \} 
\end{aligned}
$$

第一行 $f(prices[1:]$ 表示不选第 1 个数的最大利润，第二行表示选第 1 个数买入，第 2 个数卖出的最大利润，以此类推。代码如下：
```python {class=line-numbers}
class Solution(object):
    def maxProfit(self, prices):
        """
        :type prices: List[int]
        :rtype: int
        """
        if len(prices)<2:return 0
        if len(prices)==2:return max(0, prices[1]-prices[0])
        ret = 0
        for i in range(1, len(prices)):
            profit = -prices[0]+prices[i]+self.maxProfit(prices[i+1:])
            ret = max(profit, ret)
        return max(ret, self.maxProfit(prices[1:]))
```

该算法的时间复杂度非常非常高，因为要计算 n 个数的利润，需要计算后 n-1、n-2、...、2、1 个数的利润；要计算 n-1 个数的利润，需要计算后 n-2、n-3、...、2、1 个数的利润；这个过程中存在大量的重复计算，类似于求解 fabonacci 时，$f(n)=f(n-1)+f(n-2)$，如果直接用递归计算，时间复杂度是非常高的，需要将计算结果缓存下来，使得计算复杂度下降为 $O(n)$。

### Solution-2

使用缓存记录之前计算的结果，加快计算速度：
```python {class=line-numbers}
class Solution(object):
    def get_max(self, n):
        if n in self.record:
            return self.record[n]
        ret = 0
        for i in range(n-1,0,-1):
            if self.prices[-i] >= self.prices[-n]:
                ret = max(ret, self.prices[-i]-self.prices[-n]+self.get_max(i-1))
        ret = max(ret, self.get_max(n-1))
        self.record[n] = ret
        return ret
    def maxProfit(self, prices):
        """
        :type prices: List[int]
        :rtype: int
        """
        self.record = {0:0, 1:0}
        self.prices = prices
        return self.get_max(len(prices))
```
该算法的时间复杂度约为 $O(n^2)$，比之前的代码快好几个数量级，在测试含 1643 个数的最大利润时，在本地计算的时间为 253 ms，不过，在 LeetCode 上还是因为时间限制没有通过测试；

### Solution-3

利用题目的意思，总结出规律进行计算：在价格极小点处买入，在价格极大点处卖出：
```python {class=line-numbers}
class Solution(object):
    def maxProfit(self, prices):
        """
        :type prices: List[int]
        :rtype: int
        """
        if len(prices)<2:return 0
        if len(prices)==2:return max(0, prices[1]-prices[0])
        trend = -1
        ret = 0
        for i in range(1, len(prices)):
            if prices[i]>prices[i-1] and trend==-1:
                    buy_price = prices[i-1]
                    trend = 1
            elif prices[i] < prices[i-1] and trend==1:
                    ret += prices[i-1] - buy_price
                    trend = -1
        if trend==1:
            ret += prices[-1]-buy_price
        return ret
```
该算法的时间复杂度为 $O(n)$，在测试含 1643 个数的最大利润时，在本地计算的时间为 327 μs，比 Solution-2 快了 1000 倍，在 LeetCode 上通过测试。
