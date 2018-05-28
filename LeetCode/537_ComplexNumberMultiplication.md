---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 537. Complex Number Multiplication

> <https://leetcode.com/problems/complex-number-multiplication/description/>

## Description

Given two strings representing two [complex numbers](https://en.wikipedia.org/wiki/Complex_number).

You need to return a string representing their multiplication. Note i <sup>2</sup> = -1 according to the definition.

**Example 1**:
>**Input**: "1+1i", "1+1i"
**Output**: "0+2i"
**Explanation**: (1 + i) * (1 + i) = 1 + i2 + 2 * i = 2i, and you need convert it to the form of 0+2i.

**Example 2**:
>**Input**: "1+-1i", "1+-1i"
**Output**: "0+-2i"
**Explanation**: (1 - i) * (1 - i) = 1 + i2 - 2 * i = -2i, and you need convert it to the form of 0+-2i.

**Note**:

1. The input strings will not have extra blank.
1. The input strings will be given in the form of **a+bi**, where the integer **a** and **b** will both belong to the range of [-100, 100]. And **the output should be also in this form**.

## Solution

```python
class Solution:
    def complexNumberMultiply(self, a, b):
        """
        :type a: str
        :type b: str
        :rtype: str
        """
        s1, s2 = a.split('+')
        r1, i1 = int(s1), int(s2[:-1])
        s1, s2 = b.split('+')
        r2, i2 = int(s1), int(s2[:-1])
        return "{}+{}i".format(r1*r2-i1*i2,r1*i2+r2*i1)
```

