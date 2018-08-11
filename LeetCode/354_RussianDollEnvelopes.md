---
export_on_save:
 html: true
---

@import "/blog_head.md"


# 354. Russian Doll Envelopes

> <https://leetcode.com/problems/russian-doll-envelopes/submissions/1>

## Description

You have a number of envelopes with widths and heights given as a pair of integers (w, h). One envelope can fit into another if and only if both the width and height of one envelope is greater than the width and height of the other envelope.

What is the maximum number of envelopes can you Russian doll? (put one inside other)

**Note**: Rotation is not allowed.

**Example**:
**Input**: `[[5,4],[6,4],[6,7],[2,3]]`
**Output**: `3` 
**Explanation**: The maximum number of envelopes you can Russian doll is 3 (`[2,3] => [5,4] => [6,7]`).

## Solution

### Solution-1

```python {class=line-numbers}
class Solution(object):
    def maxEnvelopes(self, envelopes):
        """
        :type envelopes: List[List[int]]
        :rtype: int
        """
        import bisect
        if len(envelopes)==0: return 0
        envelopes.sort()
        record = [(-1, envelopes[-1][0], envelopes[-1][1])]
        for cur_w, cur_h in envelopes[-2::-1]:
            for v, w, h in record:
                if cur_h<h and cur_w<w:
                    cur_v = v-1
                    bisect.insort(record, (cur_v, cur_w, cur_h))
                    break
            else:
                bisect.insort(record, (-1, cur_w, cur_h))
        return -record[0][0]
```
### Solution-2
```python {class=line-numbers}
from bisect import bisect_left
class Solution:
    def maxEnvelopes(self, envelopes):
        tails = []
        for _,h in sorted(envelopes, key = lambda env : (env[0], -env[1])):
            pos = bisect_left(tails, h)
            if pos == len(tails):
                tails.append(h)
            else:
                tails[pos] = h
        return len(tails)      
```

