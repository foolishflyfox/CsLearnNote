---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 746. Min Cost Climbing Stairs

> <https://leetcode.com/problems/min-cost-climbing-stairs/description/>

## Description

On a staircase, the `i`-th step has some non-negative cost `cost[i]` assigned (0 indexed).

Once you pay the cost, you can either climb one or two steps. You need to find minimum cost to reach the top of the floor, and you can either start from the step with index 0, or the step with index 1.

**Example 1**:
**Input**: `cost = [10, 15, 20]`
**Output**: `15`
**Explanation**: Cheapest is start on cost[1], pay that cost and go to the top.

**Example 2**:
**Input**: `cost = [1, 100, 1, 1, 1, 100, 1, 1, 100, 1]`
**Output**: `6`
**Explanation**: Cheapest is start on cost[0], and only step on 1s, skipping cost[3].

**Note**:
- `cost` will have a length in the range `[2, 1000]`.
- Every `cost[i]` will be an integer in the range `[0, 999]`.

## Solution

### Solution-1

递归方式求解：
```python {class=line-numbers}
class Solution(object):
    def recursive_f(self, cur_pos):
        if len(self.cost)<=cur_pos: return 0
        if cur_pos in self.record: return self.record[cur_pos]
        cur_ret = self.cost[cur_pos]+min(self.recursive_f(cur_pos+1),
                                        self.recursive_f(cur_pos+2))
        self.record[cur_pos] = cur_ret
        return cur_ret
    def minCostClimbingStairs(self, cost):
        """
        :type cost: List[int]
        :rtype: int
        """
        if len(cost)==2: return 0
        self.cost = cost
        self.record = dict()
        return min(self.recursive_f(0),self.recursive_f(1))
```
在 LeetCode 上的测试时间为：68ms。

### Solution-2

```python {class=line-numbers}
class Solution(object):
    def minCostClimbingStairs(self, cost):
        """
        :type cost: List[int]
        :rtype: int
        """
        record = [0]*len(cost)
        record[-1], record[-2] = cost[-1], cost[-2]
        for i in range(len(cost)-3, -1, -1):
            record[i] = cost[i]+min(record[i+1], record[i+2])
        return min(record[0], record[1])
```
在 LeetCode 上的测试时间为：28ms。
