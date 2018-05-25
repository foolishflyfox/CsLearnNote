---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 119. Pascal's Triangle II

> <https://leetcode.com/problems/pascals-triangle-ii/description/>

## Description

Given a non-negative index k where k ≤ 33, return the k<sup>th</sup> index row of the Pascal's triangle.

Note that the row index starts from 0.

![](/LeetCode/PascalTriangleAnimated2.gif)

In Pascal's triangle, each number is the sum of the two numbers directly above it.

**Example**:
> **Input**: 3
**Output**: [1,3,3,1]

**Follow up**:
Could you optimize your algorithm to use only O(k) extra space?

## Solution

```python
class Solution:
    def getRow(self, rowIndex):
        """
        :type rowIndex: int
        :rtype: List[int]
        """
        def grow(rowIndex, a):
            if rowIndex==0:
                a[0] = 1
                return
            grow(rowIndex-1, a)
            pre_n = a[0]
            for i in range(1, rowIndex+1):
                a[i] += pre_n
                pre_n = a[i]-pre_n
                
        ret = [0] * (rowIndex + 1)
        grow(rowIndex, ret)
        return ret
```
