---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 209. Minimum Size Subarray Sum

> <https://leetcode.com/problems/minimum-size-subarray-sum/description/>


## Description


Given an array of n positive integers and a positive integer s, find the minimal length of a contiguous subarray of which the sum ≥ s. If there isn't one, return 0 instead.

**Example**: 
**Input**: `s = 7, nums = [2,3,1,2,4,3]`
**Output**: `2`
**Explanation**: the subarray [4,3] has the minimal length under the problem constraint.

**Follow up**: If you have figured out the O(n) solution, try coding another solution of which the time complexity is O(n log n). 

## Solution

### Solution-1

```python
class Solution(object):
    def minSubArrayLen(self, s, nums):
        """
        :type s: int
        :type nums: List[int]
        :rtype: int
        """
        if sum(nums)<s: return 0
        left, right = 1, len(nums)
        while left<right:
            mid = (left+right)//2
            v = sum(nums[:mid])
            if v >= s:
                right = mid
                continue
            for i in range(mid, len(nums)):
                v = v - nums[i-mid] + nums[i]
                if v >= s:
                    break
            else:
                left = mid+1
                continue
            right = mid
        return right
```

上述算法的时间复杂度为 $O(n\log n)$，在 LeetCode 上的测试时间为 44ms。

### Solution-2

```python
class Solution(object):
    def minSubArrayLen(self, s, nums):
        """
        :type s: int
        :type nums: List[int]
        :rtype: int
        """
        v, start, ret = 0, 0, float('inf')
        for i in range(0, len(nums)):
            v += nums[i]
            if v >= s:
                ret = min(ret, i-start+1)
                while v >= s:
                    v -= nums[start]
                    start += 1
            ret = min(ret, i-start+2)
        return 0 if ret==float('inf') else ret
```
上述算法的时间复杂度为 $O(n)$，在 LeetCode 上的测试时间为 52ms。应该是由于测试案例的数据量小，所以没有体现出时间复杂度的特性。
