---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 216. Combination Sum III

> <https://leetcode.com/problems/combination-sum-iii/description/>

## Description

Find all possible combinations of k numbers that add up to a number n, given that only numbers from 1 to 9 can be used and each combination should be a unique set of numbers.

Note:

All numbers will be positive integers.
The solution set must not contain duplicate combinations.

**Example 1**:
**Input**: `k = 3, n = 7`
**Output**: `[[1,2,4]]`

**Example 2**:
**Input**: `k = 3, n = 9`
**Output**: `[[1,2,6], [1,3,5], [2,3,4]]`

## Solution

```python
class Solution(object):
    def combinationSum3(self, k, n):
        """
        :type k: int
        :type n: int
        :rtype: List[List[int]]
        """
        from itertools import combinations
        if sum(range(9,9-k,-1))<n or n<sum(range(1,1+k)): return []
        return [list(i) for i in combinations(range(1,10),k) if sum(i)==n]
```
