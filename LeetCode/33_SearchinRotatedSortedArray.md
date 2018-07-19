---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 33. Search in Rotated Sorted Array

> <https://leetcode.com/problems/search-in-rotated-sorted-array/description/>

## Description

Suppose an array sorted in ascending order is rotated at some pivot unknown to you beforehand.

(i.e., `[0,1,2,4,5,6,7]` might become `[4,5,6,7,0,1,2]`).

You are given a target value to search. If found in the array return its index, otherwise return `-1`.

You may assume no duplicate exists in the array.

Your algorithm's runtime complexity must be in the order of O(log n).


**Example 1**:
**Input**: `nums = [4,5,6,7,0,1,2], target = 0`
**Output**: `4`

**Example 2**:
**Input**: `nums = [4,5,6,7,0,1,2], target = 3`
**Output**: `-1`

## Solution

LeetCode 上的测试时间：20ms。
```python {class=line-numbers}
class Solution(object):
    def search(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: int
        """
        if len(nums)==0: return -1
        left, right = 0, len(nums)-1
        while left<right:
            mid = (left+right)//2
            if target==nums[mid]: return mid
            if nums[mid]>=nums[left]:
                if nums[left]<=target<nums[mid]:
                    right = mid - 1
                else:
                    left = mid + 1
            else:
                if nums[mid]<target<=nums[right]:
                    left = mid + 1
                else:
                    right = mid - 1
        if right>=0 and nums[right]==target: return right
        if nums[left]==target: return left
        return -1
```
