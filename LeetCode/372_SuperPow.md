---
export_on_save:
 html: true
---

@import "/blog_head.md"


# Super Pow

> <https://leetcode.com/problems/super-pow/description/>

## Description


Your task is to calculate $a^b$ mod 1337 where a is a positive integer and b is an extremely large positive integer given in the form of an array.

**Example 1**:
>a = 2
b = [3]
Result: 8

**Example2**:
>a = 2
b = [1,0]
Result: 1024

## Solution

利用数学上的公式：$$(a\cdot b)\%c=[(k_a\cdot c+r_a)\cdot(k_b\cdot c+r_b)]\%c=r_a\cdot r_b=(a\%c)\cdot(b\%c)$$

```python
class Solution:
    def superPow(self, a, b):
        """
        :type a: int
        :type b: List[int]
        :rtype: int
        """
        factor = 1337
        remainder = a % factor
        res = 1
        for i in b[::-1]:
            res *= (remainder**i)%factor
            res %= factor
            remainder **= 10
            remainder %= factor
        return res
```

