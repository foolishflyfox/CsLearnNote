---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 845. Longest Mountain in Array

> <https://leetcode.com/problems/longest-mountain-in-array/description/>

## Description

Let's call any (contiguous) subarray B (of A) a mountain if the following properties hold:

- `B.length >= 3`
- There exists some `0 < i < B.length - 1` such that `B[0] < B[1] < ... B[i-1] < B[i] > B[i+1] > ... > B[B.length - 1]`

(Note that `B` could be any subarray of `A`, including the entire array `A`.)

Given an array `A` of integers, return the length of the longest mountain. 

Return `0` if there is no mountain.

**Example 1**:
**Input**: `[2,1,4,7,3,2,5]`
**Output**: `5`
**Explanation**: The largest mountain is `[1,4,7,3,2]` which has length 5.

**Example 2**:
**Input**: `[2,2,2]`
**Output**: `0`
**Explanation**: There is no mountain.

**Note**:
- `0 <= A.length <= 10000`
- `0 <= A[i] <= 10000`

**Follow up**:
- Can you solve it using only one pass?
- Can you solve it in `O(1)` space?

## Solution

```python{class=line-numbers}
class Solution(object):
    def longestMountain(self, A):
        """
        :type A: List[int]
        :rtype: int
        """
        if len(A)<=1: return 0
        ret = 0
        pre_foot, pre_top = -1, -1
        for i in range(1, len(A)):
            if A[i]==A[i-1]:
                pre_foot = pre_top = -1
                continue
            if pre_foot==-1:
                if A[i]>A[i-1]: pre_foot = i-1
            elif pre_top==-1:
                if A[i]<A[i-1]:
                    pre_top = i-1
                    ret = max(ret, i-pre_foot+1)
            else:
                if A[i]<A[i-1]:
                    ret = max(ret, i-pre_foot+1)
                else:
                    pre_foot = i-1
                    pre_top = -1
        return ret
```

