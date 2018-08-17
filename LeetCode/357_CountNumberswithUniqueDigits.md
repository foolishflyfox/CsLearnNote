---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 357. Count Numbers with Unique Digits

> <https://leetcode.com/problems/count-numbers-with-unique-digits/description/>

## Description

Given a non-negative integer n, count all numbers with unique digits, `x`, where 0 ≤ `x` < 10<sup>n</sup>.

**Example**:

**Input**: `2`
**Output**: `91` 
**Explanation**: The answer should be the total numbers in the range of 0 ≤ x < 100, excluding 11,22,33,44,55,66,77,88,99

## Solution

```python {class=line-numbers}
class Solution(object):
    def countNumbersWithUniqueDigits(self, n):
        """
        :type n: int
        :rtype: int
        """
        from math import factorial
        ret = 0
        for zero_cnt in range(n+1):
            if n-zero_cnt>9:
                continue
            v = n-zero_cnt+1
            if v>n: v = 1
            for i in range(n-zero_cnt):
                v *= (9-i)
            ret += v
        return ret
```
