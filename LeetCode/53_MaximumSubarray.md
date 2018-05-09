---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 53. Maximum Subarray

> <https://leetcode.com/problems/maximum-subarray/description/>

## Description

Given an integer array `nums`, find the contiguous subarray (containing at least one number) which has the largest sum and return its sum.

**Example**:

>**Input**: [-2,1,-3,4,-1,2,1,-5,4],
**Output**: 6
**Explanation**: [4,-1,2,1] has the largest sum = 6.

**Follow up**:

If you have figured out the O(n) solution, try coding another solution using the divide and conquer approach, which is more subtle.

## Solution

```python
class Solution:
    def maxSubArray(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        maxsub = None
        act_sum = 0
        min_sum = 0
        for v in nums:
            act_sum += v
            if maxsub==None:
                maxsub = act_sum
            if v > maxsub:
                maxsub = v
            if act_sum < min_sum:
                min_sum = act_sum
            else:
                if act_sum-min_sum > maxsub:
                    maxsub = act_sum-min_sum
        return maxsub
```

