---
export_on_save:
 html: true
---

@import "/blog_head.md"

#  318. Maximum Product of Word Lengths

> <https://leetcode.com/problems/maximum-product-of-word-lengths/description/>

## Description

Given a string array `words`, find the maximum value of `length(word[i]) * length(word[j])` where the two words do not share common letters. You may assume that each word will contain only lower case letters. If no such two words exist, return 0.

**Example 1**:
**Input**: `["abcw","baz","foo","bar","xtfn","abcdef"]`
**Output**: `16` 
**Explanation**: The two words can be "abcw", "xtfn".

**Example 2**:
**Input**: `["a","ab","abc","d","cd","bcd","abcd"]`
**Output**: `4` 
**Explanation**: The two words can be "ab", "cd".

**Example 3**:
**Input**: `["a","aa","aaa","aaaa"]`
**Output**: `0` 
**Explanation**: No such pair of words.

## Solution

```python {class=line-number}
class Solution(object):
    def maxProduct(self, words):
        """
        :type words: List[str]
        :rtype: int
        """
        record = sorted((-len(word), set(word)) for word in words)
        ret = 0
        for i in range(len(record)):
            if record[i][0]*record[i][0] <= ret:
                break
            for j in range(i+1, len(record)):
                if record[i][0]*record[j][0] <= ret:
                    break
                if record[i][1].isdisjoint(record[j][1]):
                    ret = record[i][0]*record[j][0]
                    break
        return ret
```
