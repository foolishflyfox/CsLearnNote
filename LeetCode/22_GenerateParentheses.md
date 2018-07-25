---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 22. Generate Parentheses

> <https://leetcode.com/problems/generate-parentheses/description/>

## Description

Given n pairs of parentheses, write a function to generate all combinations of well-formed parentheses.

For example, given n = 3, a solution set is:
```
[
  "((()))",
  "(()())",
  "(())()",
  "()(())",
  "()()()"
]
```
## Solution

```python {class=line-numbers}
class Solution(object):
    def sub_f(self, n):
        if n in self.record:
            return self.record[n]
        ret = []
        for i in range(1, n+1):
            for prefix in self.sub_f(i-1):
                prefix = '(' + prefix +')'
                ret += [prefix+postfix for postfix in self.sub_f(n-i)]
        self.record[n] = ret
        return ret
    def generateParenthesis(self, n):
        """
        :type n: int
        :rtype: List[str]
        """
        self.record = {0: [""], 1: ["()"]}
        return self.sub_f(n)
```
