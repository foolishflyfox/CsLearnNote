---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 820. Short Encoding of Words

> <https://leetcode.com/problems/short-encoding-of-words/description/>

## Description

Given a list of words, we may encode it by writing a reference string `S` and a list of indexes `A`.

For example, if the list of words is `["time", "me", "bell"]`, we can write it as `S = "time#bell#"` and `indexes = [0, 2, 5].`

Then for each index, we will recover the word by reading from the reference string from that index until we reach a "#" character.

What is the length of the shortest reference string S possible that encodes the given words?

**Example**:
**Input**: `words = ["time", "me", "bell"]`
**Output**: `10`
**Explanation**: `S = "time#bell#" and indexes = [0, 2, 5]`.

**Note**:
- `1 <= words.length <= 2000`.
- `1 <= words[i].length <= 7`.
- Each word has only lowercase letters.

## Solution

```python {class=line-numbers}
class Solution(object):
    def minimumLengthEncoding(self, words):
        """
        :type words: List[str]
        :rtype: int
        """
        import bisect
        record = []
        for word in words:
            tp_word = word[::-1]
            index = bisect.bisect_left(record, tp_word)
            if index < len(record) and record[index].startswith(tp_word):
                continue
            elif index-1>=0 and tp_word.startswith(record[index-1]):
                record[index-1] = tp_word
            else:
                record.insert(index, tp_word)
        return sum(len(s) for s in record)+len(record)
```