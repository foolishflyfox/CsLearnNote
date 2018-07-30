---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 423. Reconstruct Original Digits from English

> <https://leetcode.com/problems/reconstruct-original-digits-from-english/description/>

## Description

Given a **non-empty** string containing an out-of-order English representation of digits `0-9`, output the digits in ascending order.

**Note**:
- Input contains only lowercase English letters.
- Input is guaranteed to be valid and can be transformed to its original digits. That means invalid inputs such as "abc" or "zerone" are not permitted.
- Input length is less than 50,000.

**Example 1**:
**Input**: `"owoztneoer"`
**Output**: `"012"`

**Example 2**:
**Input**: `"fviefuro"`
**Output**: `"45"`

## Solution

### Solution-1

使用动态规划方式求解。但是在输入非常大时，迭代的层数会非常多，空间复杂度会非常大。

```python {class=line-numbers}
class Solution(object):
    def recursive_f(self, letters):
        if len(letters)==0: return True, ''
        for s_digit, counter in self.record:
            for letter in counter:
                if counter[letter]>letters[letter]:
                    break
            else:
                r,v = self.recursive_f(letters-counter)
                if r:
                    return True, s_digit+v
        else:
            return False, None
    def originalDigits(self, s):
        """
        :type s: str
        :rtype: str
        """
        from collections import Counter
        self.record = [(n,Counter(v)) for n,v in zip(list('0123456789'),
                        ['zero','one','two','three','four','five',
                         'six','seven','eight','nine'])]
        letters = Counter(s)
        r, v = self.recursive_f(letters)
        return v
```

### Solution-2

根据题目特性，比如：在 0~9 对应的字母中，只有 0（zero） 有 `z`, 因此可以先求出 0 的个数，以此类推可以大大加快求解效率。

```python {class=line-numbers}
class Solution(object):
    def originalDigits(self, s):
        """
        :type s: str
        :rtype: str
        """
        from collections import Counter
        record = [0]*10
        letters = Counter(s)
        items = [(0, 'z', 'zero'), (6, 'x', 'six'), (2, 'w', 'two'),
                 (8, 'g', 'eight'), (3, 'h', 'three'), (4, 'u', 'four'),
                 (5, 'f', 'five'), (1, 'o', 'one'), (9, 'i', 'nine'),
                 (7, 's', 'seven')]
        for n, flag, word in items:
            if letters[flag]:
                record[n] += letters[flag]
                v = letters[flag]
                for letter in word: letters[letter] -= v
        return ''.join([str(i)*v for i,v in enumerate(record)])
```
