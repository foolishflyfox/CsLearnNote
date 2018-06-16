---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 20. Valid Parentheses

> <https://leetcode.com/problems/valid-parentheses/description/>

## Description

Given a string containing just the characters `'('`, `')'`, `'{'`, `'}'`, `'['` and `']'`, determine if the input string is valid.

An input string is valid if:
- Open brackets must be closed by the same type of brackets.
- Open brackets must be closed in the correct order.

<small>Note that an empty string is also considered valid.</small>

**Example 1**:
**Input**: `"()"`
**Output**: `true`

**Example 2**:
**Input**: `"()[]{}"`
**Output**: `true`

**Example 3**:
**Input**: `"(]"`
**Output**: `false`

**Example 4**:
**Input**: `"([)]"`
**Output**: `false`

**Example 5**:
**Input**: `"{[]}"`
**Output**: `true`

## Solution

```python {class=line-numbers}
class Solution(object):
    def isValid(self, s):
        """
        :type s: str
        :rtype: bool
        """
        def addc_factory(c):
            def addc(a):
                a.append(c)
                return True
            return addc
        def popc_factory(c):
            pair_v = {']':'[', ')':'(', '}':'{'}
            def popc(a):
                if len(a)==0 or a[-1]!=pair_v[c]:
                    return False
                else:
                    a.pop()
                    return True
            return popc
        stack = []
        func_d = dict()
        for c in ['(', '{', '[']:
            func_d[c] = addc_factory(c)
        for c in [')', '}', ']']:
            func_d[c] = popc_factory(c)
        for c in s:
            if func_d[c](stack)==False:
                return False
        return len(stack)==0
```

一种更加简洁的实现：
```python {class=line-numbers}
class Solution(object):
    def isValid(self, s):
        """
        :type s: str
        :rtype: bool
        """
        c_dict = {'(':')', '[':']', '{':'}'}
        stack = []
        for c in s:
            if c in c_dict:
                stack.append(c)
            elif len(stack)==0 or c_dict[stack.pop()]!=c:
                return False
        return len(stack)==0
```


