---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 462. Minimum Moves to Equal Array Elements II

> <https://leetcode.com/problems/minimum-moves-to-equal-array-elements-ii/description/>

## Description

Given a **non-empty** integer array, find the minimum number of moves required to make all array elements equal, where a move is incrementing a selected element by 1 or decrementing a selected element by 1.

You may assume the array's length is at most 10,000.

**Example**:

>**Input**: `[1,2,3]`
**Output**: `2`
**Explanation**:
Only two moves are needed (remember each move increments or decrements one element):
`[1,2,3]  =>  [2,2,3]  =>  [2,2,2]`

## Solution

### Solution - 1

```python
class Solution:
    def minMoves2(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        nums.sort()
        nums_len = len(nums)
        if nums_len%2:
            median = nums[nums_len//2]
        else:
            median = (nums[nums_len//2]+nums[nums_len//2-1])//2
        return sum(abs(i-median) for i in nums)
```

### Solution - 2

```python
class Solution:
    def minMoves2(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        nums.sort()
        return sum(nums[~i]-nums[i] for i in range(len(nums)//2))
```


