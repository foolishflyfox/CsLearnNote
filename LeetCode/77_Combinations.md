---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 77. Combinations

> <https://leetcode.com/problems/combinations/description/>

## Description

Given two integers n and k, return all possible combinations of k numbers out of 1 ... n.

**Example**:
**Input**: `n = 4, k = 2`
**Output**:
```
[
  [2,4],
  [3,4],
  [2,3],
  [1,2],
  [1,3],
  [1,4],
]
```

## Solution

### Solution-1

使用内置的库函数解决：
```python {class=line-numbers}
class Solution(object):
    def combine(self, n, k):
        """
        :type n: int
        :type k: int
        :rtype: List[List[int]]
        """
        from itertools import combinations
        return [list(item) for item in combinations(range(1, n+1), k)]
```

### Solution-2

```python {class=line-numbers}
class Solution(object):
    def recursive_f(self, cur_pos):
        if len(self.record)==self.k:
            self.ret.append(self.record[:])
            return
        if len(self.record)+self.n-cur_pos+1<self.k:
            return
        self.record.append(cur_pos)
        self.recursive_f(cur_pos+1)
        self.record.pop()
        self.recursive_f(cur_pos+1)
        
    def combine(self, n, k):
        """
        :type n: int
        :type k: int
        :rtype: List[List[int]]
        """
        self.ret, self.record = [], []
        self.n, self.k = n, k
        self.recursive_f(1)
        return self.ret
```

