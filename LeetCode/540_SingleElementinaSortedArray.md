---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 540. Single Element in a Sorted Array

## Description

Given a sorted array consisting of only integers where every element appears twice except for one element which appears once. Find this single element that appears only once.

**Example 1**:
**Input**: `[1,1,2,3,3,4,4,8,8]`
**Output**: `2`

**Example 2**:
**Input**: `[3,3,7,7,10,11,11]`
**Output**: `10`

**Note**: Your solution should run in $O(\log n)$ time and $O(1)$ space.

## Solution

```python {class=line-numbers}
class Solution(object):
    def singleNonDuplicate(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        left, right = 0, len(nums)-1
        while left<right:
            mid = (left+right)//2
            if mid%2:
                if nums[mid]==nums[mid+1]:
                    right = mid - 1
                elif mid-1>=0 and nums[mid]==nums[mid-1]:
                    left = mid + 1
                else:
                    return nums[mid]
            else:
                if nums[mid]==nums[mid+1]:
                    left = mid + 2
                elif mid-1>=0 and nums[mid]==nums[mid-1]:
                    right = mid + 1
                else:
                    return nums[mid]
        return nums[left]
```


