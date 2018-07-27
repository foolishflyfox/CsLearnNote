---
export_on_save:
 html: true
---
@import "/blog_head.md"

639. Decode Ways II

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

注意：下面这个例子是错误的。
**Example 2**:
**Input**: `"1*"`
**Output**: 9 + 9 = 18

正确的应该是 19，可以解码的方式为：`[1,1], [1,2], [1,3], [1,4], [1,5], [1,6], [1,7], [1,8], [1,9], [10], [11], [12], [13], [14], [15], [16], [17], [18], [19]`。因此，我认为 LeetCode 上所给出的评判标准也是错误的。

**Note**:
- The length of the input string will fit in range `[1, 105]`.
- The input string will only contain the character `'*'` and digits `'0' - '9'`.

## Solution

### Solution-1

递归调用方式：
```python {class=line-numbers}
class Solution(object):
    def recursive_f(self, start):
        if self.s[start:]=='': return 1
        if start in self.record: return self.record[start]
        if len(self.s)-start==1:
            if self.s[start]=='*': return 9
            else: return 1
        if self.s[start]=='*':
            ret = 9*self.recursive_f(start+1)
            if self.s[start+1]=='*': ret += 17 * self.recursive_f(start+2)
            elif self.s[start+1]<='6': ret += 2 * self.recursive_f(start+2)
            else: ret += self.recursive_f(start+2)
        elif self.s[start]=='1':
            ret = self.recursive_f(start+1)
            if self.s[start+1]=='*': ret += 10*self.recursive_f(start+2)
            else: ret += self.recursive_f(start+2)
        elif self.s[start]=='2':
            ret = self.recursive_f(start+1)
            if self.s[start+1]=='*': ret += 7*self.recursive_f(start+2)
            elif self.s[start+1]<='6': ret += self.recursive_f(start+2)
        else:
            ret = self.recursive_f(start+1)
        ret %= self.mod_val
        self.record[start] = ret
        return ret
    def numDecodings(self, s):
        """
        :type s: str
        :rtype: int
        """
        self.mod_val = 10**9 + 7
        s_list, a = list(s), []
        ret = 1
        for i in range(len(s)):
            if s[i]!='0': a.append(s[i])
            else:
                if len(a)==0: return 0
                if s[i-1]=='*':
                    ret *= 2
                if s[i-1]>='3':
                    return 0
                a.pop()
        self.s = ''.join(a)
        if self.s=='': return ret % self.mod_val
        self.record = dict()
        return self.recursive_f(0)
```
上述代码在 `s` 非常长的时候，可能会发生栈溢出，下面的代码将递归改成 `for` 迭代。

### Solution-2

```python {class=line-numbers}
class Solution(object):
    def numDecodings(self, s):
        """
        :type s: str
        :rtype: int
        """
        self.mod_val = 10**9 + 7
        s_list, a = list(s), []
        ret = 1
        for i in range(len(s)):
            if s[i]!='0': a.append(s[i])
            else:
                if len(a)==0: return 0
                if s[i-1]=='*':
                    ret *= 2
                if s[i-1]>='3':
                    return 0
                a.pop()
        self.s = ''.join(a)
        if self.s=='': return ret % self.mod_val
        self.record = {len(self.s):1}
        for i in range(len(self.s)-1, -1, -1):
            if len(self.s)-i==1:
                if self.s[i]=='*': self.record[i] = 9
                else: self.record[i] = 1
            elif self.s[i]=='*':
                tp_ret = 9*self.record[i+1]
                if self.s[i+1]=='*': tp_ret += 17 * self.record[i+2]
                elif self.s[i+1]<='6': tp_ret += 2 * self.record[i+2]
                else: tp_ret += self.record[i+2]
                self.record[i] = tp_ret%self.mod_val
            elif self.s[i]=='1':
                tp_ret = self.record[i+1]
                if self.s[i+1]=='*': tp_ret += 10*self.record[i+2]
                else: tp_ret += self.record[i+2]
                self.record[i] = tp_ret%self.mod_val
            elif self.s[i]=='2':
                tp_ret = self.record[i+1]
                if self.s[i+1]=='*': tp_ret += 7*self.record[i+2]
                elif self.s[i+1]<='6': tp_ret += self.record[i+2]
                self.record[i] = tp_ret%self.mod_val
            else:
                self.record[i] = self.record[i+1]
        return self.record[0]
```
