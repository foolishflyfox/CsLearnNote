---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 387. First Unique Character in a String

> <https://leetcode.com/problems/first-unique-character-in-a-string/description/>

## Description

Given a string, find the first non-repeating character in it and return it's index. If it doesn't exist, return -1.

**Examples**:

>s = "leetcode"
return 0.

>s = "loveleetcode",
return 2.

**Note**: You may assume the string contain only lowercase letters.

## Solution

```python {class="line-numbers"}
class Solution:
    def firstUniqChar(self, s):
        """
        :type s: str
        :rtype: int
        """
        c_dict = dict()
        c_set = set()
        for i in range(len(s)):
            c = s[i]
            if c in c_dict:
                del c_dict[c]
                continue
            if c in c_set:
                continue
            c_set.add(c)
            c_dict[c] = i
        if len(c_dict):
            return min(v for v in c_dict.values())
        else:
            return -1
```

