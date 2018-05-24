---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 451. Sort Characters By Frequency

> <https://leetcode.com/problems/sort-characters-by-frequency/description/>

## Description

Given a string, sort it in decreasing order based on the frequency of characters.

**Example 1**:

>**Input**:
"tree"
**Output**:
"eert"
**Explanation**:
'e' appears twice while 'r' and 't' both appear once.
So 'e' must appear before both 'r' and 't'. Therefore "eetr" is also a valid answer.

**Example 2**:

>**Input**:
"cccaaa"
**Output**:
"cccaaa"
**Explanation**:
Both 'c' and 'a' appear three times, so "aaaccc" is also a valid answer.
Note that "cacaca" is incorrect, as the same characters must be together.

**Example 3**:

>**Input**:
"Aabb"
**Output**:
"bbAa"
**Explanation**:
"bbaA" is also a valid answer, but "Aabb" is incorrect.
Note that 'A' and 'a' are treated as two different characters.

## Solution

```python
class Solution:
    def frequencySort(self, s):
        """
        :type s: str
        :rtype: str
        """
        cs_dict = dict()
        for c in s:
            cs_dict[c] = cs_dict.setdefault(c, 0) + 1
        cs_list = [(n, c) for c,n in cs_dict.items()]
        cs_list.sort(reverse=True)
        return ''.join(c*n for n,c in cs_list)
```


