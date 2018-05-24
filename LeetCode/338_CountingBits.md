---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 338. Counting Bits

> <https://leetcode.com/problems/counting-bits/description/>

## Description

Given a non negative integer number num. For every numbers i in the range **0 ≤ i ≤ num** calculate the number of 1's in their binary representation and return them as an array.

**Example**:
For `num = 5` you should return `[0,1,1,2,1,2]`.

**Follow up**:

- It is very easy to come up with a solution with run time **O(n\*sizeof(integer))**. But can you do it in linear time **O(n)** /possibly in a single pass?
- Space complexity should be **O(n)**.
- Can you do it like a boss? Do it without using any builtin function like **__builtin_popcount** in c++ or in any other language.

## Solution

```python
class Solution:
    def countBits(self, num):
        """
        :type num: int
        :rtype: List[int]
        """
        results = [0]*(num+1)
        i = 1
        while i < num+1:
            for j in range(i, min(i*2, num+1)):
                results[j] = results[j-i]+1
            i *= 2
        return results
```

