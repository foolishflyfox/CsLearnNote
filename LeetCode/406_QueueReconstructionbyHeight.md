---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 406. Queue Reconstruction by Height

> <https://leetcode.com/problems/queue-reconstruction-by-height/description/>


## Description


Suppose you have a random list of people standing in a queue. Each person is described by a pair of integers `(h, k)`, where `h` is the height of the person and `k` is the number of people in front of this person who have a height greater than or equal to `h`. Write an algorithm to reconstruct the queue.

**Note**:
The number of people is less than 1,100.


**Example**
**Input**:
`[[7,0], [4,4], [7,1], [5,0], [6,1], [5,2]]`

**Output**:
`[[5,0], [7,0], [5,2], [6,1], [4,4], [7,1]]`

## Solution

```python {class=line-numbers}
class Solution(object):
    def reconstructQueue(self, people):
        """
        :type people: List[List[int]]
        :rtype: List[List[int]]
        """
        from collections import defaultdict
        ret = []
        record = defaultdict(list)
        for h, k in people:
            record[h].append(k)
        for i in record.keys():
            record[i].sort()
        for h in sorted(record.keys(), reverse=True):
            for k in record[h]:
                ret.insert(k, [h, k])
        return ret
```

