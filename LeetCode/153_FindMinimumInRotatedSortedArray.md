---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 153. Find Minimum in Rotated Sorted Array

> <https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/description/>

## Description

Suppose an array sorted in ascending order is rotated at some pivot unknown to you beforehand.

(i.e.,  `[0,1,2,4,5,6,7]` might become  `[4,5,6,7,0,1,2]`).

Find the minimum element.

You may assume no duplicate exists in the array.


**Example 1**:

>**Input**: `[3,4,5,1,2]`
**Output**: `1`

**Example 2**:

>**Input**: `[4,5,6,7,0,1,2]`
**Output**: `0`

## Solution

```python
class Solution:
    def findMin(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        if len(nums)==0:
            return None
        left = 0
        right = len(nums)-1
        while left<right:
            if nums[left]<nums[right]:
                break
            else:
                mid = (left+right)//2
                if nums[mid] < nums[left]:
                    right = mid
                elif nums[mid] > nums[left]:
                    left = mid
                else:
                    if nums[left] > nums[right]:
                        left = right
        return nums[left]
```
