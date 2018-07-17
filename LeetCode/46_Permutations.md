---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 46. Permutations

> <https://leetcode.com/problems/permutations/description/>

## Description

Given a collection of distinct integers, return all possible permutations.

**Example**:

**Input**: `[1,2,3]`
**Output**:
```
[
  [1,2,3],
  [1,3,2],
  [2,1,3],
  [2,3,1],
  [3,1,2],
  [3,2,1]
]
```
## Solution

### Solution-1

```python {class=line-numbers}
class Solution(object):
    def get_permute(self, n):
        if n==len(self.nums):
            self.ret.append(self.nums[:])
            return
        for i in range(n, len(self.nums)):
            self.nums[i], self.nums[n] = self.nums[n], self.nums[i]
            self.get_permute(n+1)
            self.nums[i], self.nums[n] = self.nums[n], self.nums[i]
    def permute(self, nums):
        """
        :type nums: List[int]
        :rtype: List[List[int]]
        """
        self.nums = nums
        self.ret = []
        self.get_permute(0)
        return self.ret
```

### Solution-2

```python {class=line-numbers}
class Solution(object):
    def get_permute(self, ns):
        if len(ns)==0:
            return [[]]
        ret = []
        for i in range(len(ns)):
            remain = self.get_permute(ns[:i]+ns[i+1:])
            for r in remain:
                ret.append([ns[i]]+r)
        return ret
    def permute(self, nums):
        """
        :type nums: List[int]
        :rtype: List[List[int]]
        """
        return self.get_permute(nums)
```

### Solution-3

```python {class=line-numbers}
class Solution(object):
    def permute(self, nums):
        """
        :type nums: List[int]
        :rtype: List[List[int]]
        """
        from itertools import permutations
        return [list(i) for i in permutations(nums)]
```

