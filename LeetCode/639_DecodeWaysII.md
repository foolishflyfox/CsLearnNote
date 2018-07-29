---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 639. Decode Ways II

> <https://leetcode.com/problems/decode-ways-ii/description/>

## Description

A message containing letters from `A-Z` is being encoded to numbers using the following mapping way:
```
'A' -> 1
'B' -> 2
...
'Z' -> 26
```
Beyond that, now the encoded string can also contain the character '*', which can be treated as one of the numbers from 1 to 9.

Given the encoded message containing digits and the character '*', return the total number of ways to decode it.

Also, since the answer may be very large, you should return the output mod 109 + 7.

**Example 1**:
**Input**: `"*"`
**Output**: `9`
**Explanation**: The encoded message can be decoded to the string: `"A", "B", "C", "D", "E", "F", "G", "H", "I"`.
**Example 2**:
**Input**: `"1*"`
**Output**: 9 + 9 = 18


**Note**:
- The length of the input string will fit in range `[1, 105]`.
- The input string will only contain the character `'*'` and digits `'0' - '9'`.

## Solution

```python {class=line-numbers}
class Solution(object):
    def decoding_without_0(self, s):
        if s=='': return 1
        self.record = {len(s):1}
        for i in range(len(s)-1, -1, -1):
            if len(s)-i==1:
                if s[i]=='*': self.record[i] = 9
                else: self.record[i] = 1
            elif s[i]=='*':
                tp_ret = 9*self.record[i+1]
                if s[i+1]=='*': tp_ret += 15 * self.record[i+2]
                elif s[i+1]<='6': tp_ret += 2 * self.record[i+2]
                else: tp_ret += self.record[i+2]
                self.record[i] = tp_ret%self.mod_val
            elif s[i]=='1':
                tp_ret = self.record[i+1]
                if s[i+1]=='*': tp_ret += 9*self.record[i+2]
                else: tp_ret += self.record[i+2]
                self.record[i] = tp_ret%self.mod_val
            elif s[i]=='2':
                tp_ret = self.record[i+1]
                if s[i+1]=='*': tp_ret += 6*self.record[i+2]
                elif s[i+1]<='6': tp_ret += self.record[i+2]
                self.record[i] = tp_ret%self.mod_val
            else:
                self.record[i] = self.record[i+1]
        return self.record[0]%self.mod_val
    def numDecodings(self, s):
        """
        :type s: str
        :rtype: int
        """
        if len(s)==0: return 0
        self.mod_val = 10**9 + 7
        s_list, a = list(s), []
        ret = 1
        ss = []
        for i in range(len(s)):
            if s[i]!='0': a.append(s[i])
            else:
                if len(a)==0: return 0
                if s[i-1]=='*':
                    ret *= 2
                if s[i-1]>='3':
                    return 0
                a.pop()
                ss.append(''.join(a))
                a = []
        else:
            ss.append(''.join(a))
        for s in ss:
            ret =  ret * self.decoding_without_0(s) % self.mod_val
        return ret
```
