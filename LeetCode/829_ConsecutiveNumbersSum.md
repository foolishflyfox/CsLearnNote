---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 829. Consecutive Numbers Sum

> <https://leetcode.com/problems/consecutive-numbers-sum/description/>

## Description

Given a positive integer `N`, how many ways can we write it as a sum of consecutive positive integers?

**Example 1**:
**Input**: `5`
**Output**: `2`
**Explanation**: `5 = 5 = 2 + 3`

**Example 2**:
**Input**: `9`
**Output**: `3`
**Explanation**: `9 = 9 = 4 + 5 = 2 + 3 + 4`

**Example 3**:
**Input**: `15`
**Output**: `4`
**Explanation**: `15 = 15 = 8 + 7 = 4 + 5 + 6 = 1 + 2 + 3 + 4 + 5`

**Note**: `1 <= N <= 10 ^ 9.`

## Solution

```python
class Solution(object):
    def consecutiveNumbersSum(self, N):
        """
        :type N: int
        :rtype: int
        """
        ret = 1
        value = 2
        while True:
            if value%2:
                if N//value-value//2<1: break
                if N%value==0:
                    ret += 1
            else:
                if N//value-value//2+1<1: break
                if N%(N//value*2+1)==0:
                    ret += 1
            value += 1
        return ret
```
