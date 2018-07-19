---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 201. Bitwise AND of Numbers Range

> <https://leetcode.com/problems/bitwise-and-of-numbers-range/description/>

## Description

Given a range `[m, n]` where `0 <= m <= n <= 2147483647`, return the bitwise AND of all numbers in this range, inclusive.


**Example 1**:
**Input**: `[5,7]`
**Output**: `4`

**Example 2**:
**Input**: `[0,1]`
**Output**: `0`

## Solution

### Solution-1

执行时间：104ms

```python
class Solution(object):
    def rangeBitwiseAnd(self, m, n):
        """
        :type m: int
        :type n: int
        :rtype: int
        """
        n_s = list(format(n, 'b'))
        ret_s = ['0'] * len(n_s)
        for i in range(len(n_s)-1,-1,-1):
            if n_s[i] == '1':
                n_s[i] = '0'
                if int(''.join(n_s), base=2) < m:
                    ret_s[i] = '1'
            n_s[i] = '1'
        return int(''.join(ret_s), base=2)
```

### Solution-2

执行时间：100ms

```python
class Solution(object):
    def rangeBitwiseAnd(self, m, n):
        """
        :type m: int
        :type n: int
        :rtype: int
        """
        n_s = list(format(n, 'b'))
        n_len = len(n_s)
        ret_s = ['0'] * n_len
        for i in range(n_len-1,-1,-1):
            if n_s[i] == '1':
                n -= 2**(n_len-1-i)
                if n < m:
                    ret_s[i] = '1'
            n |= 2**(n_len-1-i)
        return int(''.join(ret_s), base=2)
```

### Solution-3

执行时间：88ms
```python
class Solution(object):
    def rangeBitwiseAnd(self, m, n):
        """
        :type m: int
        :type n: int
        :rtype: int
        """
        n_s = list(format(n, 'b'))
        ret_s = ['0'] * len(n_s)
        x = 1
        for i in range(len(n_s)-1,-1,-1):
            if n_s[i] == '1':
                n -= x
                if n < m:
                    ret_s[i] = '1'
            n |= x
            x *= 2
        return int(''.join(ret_s), base=2)
```
