---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 763. Partition Labels

> <https://leetcode.com/problems/partition-labels/description/>

## Description

A string `S` of lowercase letters is given. We want to partition this string into as many parts as possible so that each letter appears in at most one part, and return a list of integers representing the size of these parts.

**Example 1**:
**Input**: S = "ababcbacadefegdehijhklij"
**Output**: [9,7,8]
**Explanation**:
The partition is "ababcbaca", "defegde", "hijhklij".
This is a partition so that each letter appears in at most one part.
A partition like "ababcbacadefegde", "hijhklij" is incorrect, because it splits S into less parts.

**Note**:

- `S` will have length in range `[1, 500]`.
- `S` will consist of lowercase letters (`'a'` to `'z'`) only.

## Solution

```python
class Solution:
    def partitionLabels(self, S):
        """
        :type S: str
        :rtype: List[int]
        """
        record = dict()
        for i,c in enumerate(S):
            if c not in record:
                record[c] = [i, i]
            else:
                record[c][-1] = i
        merge_r = []
        for k in record.keys():
            for i in range(len(merge_r)):
                if not (merge_r[i][1]<record[k][0] or
                   merge_r[i][0]>record[k][1]):
                    merge_r[i][0] = min(merge_r[i][0], record[k][0])
                    merge_r[i][1] = max(merge_r[i][1], record[k][1])
                    break
            else:
                merge_r.append(record[k])
        merge_r.sort()
        return [rg[1]-rg[0]+1 for rg in merge_r]
    
s = Solution()
s.partitionLabels("eccbbbbdec")
```

最后，真的要吐槽一下 LeetCode 的 judge 系统，又出现误判。。。


