---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 93. Restore IP Addresses

> <https://leetcode.com/problems/restore-ip-addresses/description/>


## Description

Given a string containing only digits, restore it by returning all possible valid IP address combinations.

**Example**:
**Input**: "25525511135"
**Output**: ["255.255.11.135", "255.255.111.35"]


## Solution

```python
class Solution(object):
    def restoreIpAddresses(self, s):
        """
        :type s: str
        :rtype: List[str]
        """
        from itertools import combinations
        if len(s)>12: return []
        ret = []
        for p1, p2, p3 in combinations(range(1, len(s)), 3):
            if (s[0]=='0' and p1>1) or int(s[:p1])>255: continue
            if (s[p1]=='0' and p2-p1>1) or int(s[p1:p2])>255: continue
            if (s[p2]=='0' and p3-p2>1) or int(s[p2:p3])>255: continue
            if (s[p3]=='0' and len(s)-p3>1) or int(s[p3:])>255: continue
            ret.append('.'.join([s[:p1],s[p1:p2],s[p2:p3],s[p3:]]))
        return ret
```
