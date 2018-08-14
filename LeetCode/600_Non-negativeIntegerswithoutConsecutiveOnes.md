---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 600. Non-negative Integers without Consecutive Ones

> <https://leetcode.com/problems/non-negative-integers-without-consecutive-ones/description/>

## Description

Given a positive integer n, find the number of non-negative integers less than or equal to n, whose binary representations do NOT contain consecutive ones.

**Example 1**:
**Input**: `5`
**Output**: `5`
**Explanation**:  Here are the non-negative integers <= 5 with their corresponding binary representations:
```
0 : 0
1 : 1
2 : 10
3 : 11
4 : 100
5 : 101
```
Among them, only integer 3 disobeys the rule (two consecutive ones) and the other 5 satisfy the rule. 

**Note**: `1 <= n <= 109`

## Solution

```python {class=line-numbers}
from math import factorial
class Solution(object):
    def recursive_f(self, s):
        if len(s)==0: return 0
        if len(s)==1:
            if s=='0': return 1
            if s=='1': return 2
        if len(s)==2: return 3
        if s[:2]=='11':
            ret = 0
            for one_count in range(len(s)):
                zero_count = len(s)-one_count
                if one_count > zero_count+1:
                    break
                ret += (factorial(zero_count+1)//factorial(
                    one_count)//factorial(zero_count+1-one_count))
            return ret
        ret = self.recursive_f("1"*(len(s)-1))
        i = 1
        while i<len(s) and s[i]=='0':
            i+=1
        if i < len(s):
            ret += self.recursive_f(s[i:])
        else:
            ret += 1
        return ret
    def findIntegers(self, num):
        """
        :type num: int
        :rtype: int
        """
        return self.recursive_f(str.format('{:b}', num))
```
