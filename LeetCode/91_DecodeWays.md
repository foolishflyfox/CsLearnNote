---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 91. Decode Ways

> <https://leetcode.com/problems/decode-ways/description/>

## Description

A message containing letters from `A-Z` is being encoded to numbers using the following mapping:

```
'A' -> 1
'B' -> 2
...
'Z' -> 26
```
Given a **non-empty** string containing only digits, determine the total number of ways to decode it.

**Example 1**:

**Input**: "12"
**Output**: 2
**Explanation**: It could be decoded as "AB" (1 2) or "L" (12).

**Example 2**:
**Input**: "226"
**Output**: 3
**Explanation**: It could be decoded as "BZ" (2 26), "VF" (22 6), or "BBF" (2 2 6).

## Solution

```python
class Solution(object):
    def num_d(self, s):
        if len(s)<=1: return 1
        # 图搜索 避免重复计算
        if s in self.record: return self.record[s]
        for i in range(len(s)):
            if int(s[i:i+2])<27:
                break
        if i==len(s)-1: return 1
        ret = self.num_d(s[i+1:])+self.num_d(s[i+2:])
        self.record[s] = ret
        return ret
    def numDecodings(self, s):
        """
        :type s: str
        :rtype: int
        """
        if s[0]=='0': return 0
        new_s = [s[0]]
        # 字符串预处理 消除其中的 0，使后续逻辑更加简单
        for i in range(1, len(s)):
            if s[i]=='0':
                if s[i-1]!='2' and s[i-1]!='1': return 0
                else: new_s.pop()
            else:
                new_s.append(s[i])
        self.record = dict()
        return self.num_d(''.join(new_s))
```
