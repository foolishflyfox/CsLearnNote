---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 162. Find Peak Element

> <https://leetcode.com/problems/find-peak-element/description/>

## Description

A peak element is an element that is greater than its neighbors.

Given an input array `nums`, where `nums[i] ≠ nums[i+1]`, find a peak element and return its index.

The array may contain multiple peaks, in that case return the index to any one of the peaks is fine.

You may imagine that `nums[-1] = nums[n] = -∞`.

**Example 1**:

>**Input**: nums = [1,2,3,1]
**Output**: 2
**Explanation**: 3 is a peak element and your function should return the index number 2.

**Example 2**:

> **Input**: nums = [1,2,1,3,5,6,4]
**Output**: 1 or 5 
**Explanation**: Your function can return either index number 1 where the peak element is 2, or index number 5 where the peak element is 6.

**Note**: Your solution should be in **logarithmic complexity**.

## Solution

```python
class Solution:
    def findPeakElement(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        def findpeak(nums, left, right):
            if left==right:
                return left
            if left==right-1:
                return left if nums[left]>nums[right] else right
            mid = (left+right)//2
            if nums[mid] <= nums[left]:
                return findpeak(nums, left, mid-1)
            if nums[mid] <= nums[right]:
                return findpeak(nums, mid+1, right)
            l_peak = findpeak(nums, left, mid)
            if l_peak != mid:
                return l_peak
            r_peak = findpeak(nums, mid, right)
            if r_peak != mid:
                return r_peak
            return mid
        
        return findpeak(nums, 0, len(nums)-1)
```

用时 36 ms，超过 99.34% 的运行时间。


