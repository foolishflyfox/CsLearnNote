---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 438. Find All Anagrams in a String

> <https://leetcode.com/problems/find-all-anagrams-in-a-string/description/>

## Description

Given a string s and a **non-empty** string **p**, find all the start indices of **p**'s anagrams in **s**.

Strings consists of lowercase English letters only and the length of both strings **s** and **p** will not be larger than 20,100.

The order of output does not matter.


**Example 1**:
**Input**: `s: "cbaebabacd" p: "abc"`
**Output**: `[0, 6]`

**Explanation**:
The substring with start index = 0 is "cba", which is an anagram of "abc".
The substring with start index = 6 is "bac", which is an anagram of "abc".

**Example 2**:
**Input**: `s: "abab" p: "ab"`
**Output**: `[0, 1, 2]`

**Explanation**:
The substring with start index = 0 is "ab", which is an anagram of "ab".
The substring with start index = 1 is "ba", which is an anagram of "ab".
The substring with start index = 2 is "ab", which is an anagram of "ab".

## Solution

```python {class=line-numbers}
from collections import Counter
class Solution(object):
    def counter_distance(self, s2):
        counter1, counter2 = self.p_counter, Counter(s2)
        ret = 0
        for c in counter1:
            ret += abs(counter1[c]-counter2[c])
        if ret==0: return ret
        for i in range(len(s2)-1, -1, -1):
            if s2[i] not in counter1:
                break
        return max(ret, 2*(i+1))
    def findAnagrams(self, s, p):
        """
        :type s: str
        :type p: str
        :rtype: List[int]
        """
        self.p_counter = Counter(p)
        ret = []
        i = 0
        while i <= len(s)-len(p):
            distance = self.counter_distance(s[i:i+len(p)])
            if distance==0:
                ret.append(i)
                i += 1
                while i <= len(s)-len(p):
                    if s[i-1]==s[i+len(p)-1]:
                        ret.append(i)
                        i += 1
                    else:
                        break
            else:
                i += (distance+1)//2
        return ret
```

