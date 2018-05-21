---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 49. Group Anagrams

> <https://leetcode.com/problems/group-anagrams/description/>

## Description

Given an array of strings, group anagrams together.

**Example**:

**Input**: 
```
["eat", "tea", "tan", "ate", "nat", "bat"]
```
**Output**:
```
[
  ["ate","eat","tea"],
  ["nat","tan"],
  ["bat"]
]
```
**Note**:

- All inputs will be in lowercase.
- The order of your output does not matter.

## Solution

```python
class Solution:
    def groupAnagrams(self, strs):
        """
        :type strs: List[str]
        :rtype: List[List[str]]
        """
        words = dict()
        for s in strs:
            tp_s = ''.join(sorted(s))
            words.setdefault(tp_s, []).append(s)
        return [s for s in words.values()]
```
