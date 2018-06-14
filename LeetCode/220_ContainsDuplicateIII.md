---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 220. Contains Duplicate III

> <https://leetcode.com/problems/contains-duplicate-iii/description/>

## Description

Given an array of integers, find out whether there are two distinct indices *i* and *j* in the array such that the **absolute** difference between **nums[i]** and **nums[j]** is at most *t* and the **absolute** difference between *i* and *j* is at most *k*.


**Example 1**:
>**Input**: nums = `[1,2,3,1]`, k = `3`, t = `0`
**Output**: `true`

**Example 2**:
>**Input**: nums = `[1,0,1,1]`, k = `1`, t = `2`
**Output**: `true`

**Example 3**:
>**Input**: nums = `[1,5,9,1,5,9]`, k = `2`, t = `3`
**Output**: `false`

## Solutions

### Solutions-1
```python
class Solution(object):
    def containsNearbyAlmostDuplicate(self, nums, k, t):
        """
        :type nums: List[int]
        :type k: int
        :type t: int
        :rtype: bool
        """
        ret = False
        nums_len = len(nums)
        if k > nums_len:
            k = nums_len
        for i in range(k):
            for j in range(i+1, k):
                if abs(nums[i]-nums[j])<=t:
                    ret = True
                    break
            if ret:
                break
        if ret:return True
        for i in range(k, nums_len):
            for j in range(1, k+1):
                if abs(nums[i]-nums[i-j])<=t:
                    ret = True
                    break
            if ret:
                break
        return ret
```
时间复杂度：$O(n\cdot k)$，其中 $n$ 是 `nums` 的长度。

结果：Time Limit Error

### Solution-2

```python
class Solution(object):
    def containsNearbyAlmostDuplicate(self, nums, k, t):
        """
        :type nums: List[int]
        :type k: int
        :type t: int
        :rtype: bool
        """
        import bisect
        ret = False
        nums_len = len(nums)
        buf = nums[:k+1]
        buf.sort()
        for i in range(1,len(buf)):
            if buf[i]-buf[i-1]<=t:
                return True
        for i in range(k+1, nums_len):
            del buf[bisect.bisect_left(buf, nums[i-k-1])]
            nearest = bisect.bisect_left(buf, nums[i])
            if ((nearest<k and buf[nearest]-nums[i]<=t) or 
                (nearest-1>=0 and nums[i]-buf[nearest-1]<=t)):
                ret = True
                break
            bisect.insort(buf, nums[i])
        return ret
```
时间复杂度：$O(n\cdot\log k)$
结果：112ms，优于 17.18% 的提交

### Solution-3
添加对 `k=0` 和 `t=0` 的检测：
```python
class Solution(object):
    def containsNearbyAlmostDuplicate(self, nums, k, t):
        """
        :type nums: List[int]
        :type k: int
        :type t: int
        :rtype: bool
        """
        if k==0:return False
        if t==0:return len(set(nums))!=len(nums)
        import bisect
        ret = False
        nums_len = len(nums)
        buf = nums[:k+1]
        buf.sort()
        for i in range(1,len(buf)):
            if buf[i]-buf[i-1]<=t:
                return True
        for i in range(k+1, nums_len):
            del buf[bisect.bisect_left(buf, nums[i-k-1])]
            nearest = bisect.bisect_left(buf, nums[i])
            if ((nearest<k and buf[nearest]-nums[i]<=t) or 
                (nearest-1>=0 and nums[i]-buf[nearest-1]<=t)):
                ret = True
                break
            bisect.insort(buf, nums[i])
        return ret
```
结果：48ms，优于 81.5% 的提交
结论：和 Solution-2 的时间复杂度相同，但在 LeetCode 的运行时间大大减小，可见 LeetCode 上的运行时间并不能作为衡量算法时间复杂度的依据

### Solution-4
使用尽可能快的返回的策略，不按照软件工程中的规范：
```python
class Solution(object):
    def containsNearbyAlmostDuplicate(self, nums, k, t):
        """
        :type nums: List[int]
        :type k: int
        :type t: int
        :rtype: bool
        """
        if k==0:return False
        if t==0:return len(set(nums))!=len(nums)
        import bisect
        nums_len = len(nums)
        buf = nums[:k+1]
        buf.sort()
        for i in range(1,len(buf)):
            if buf[i]-buf[i-1]<=t:
                return True
        for i in range(k+1, nums_len):
            del buf[bisect.bisect_left(buf, nums[i-k-1])]
            nearest = bisect.bisect_left(buf, nums[i])
            if ((nearest<k and buf[nearest]-nums[i]<=t) or 
                (nearest-1>=0 and nums[i]-buf[nearest-1]<=t)):
                return True
            bisect.insort(buf, nums[i])
        return False
```
结果：33ms，优于 100% 的提交
结论：LeetCode 上测评的是算法性能，而软件工程上考虑的是可扩展性、易读性，两者的目标不一致，所以，使用软件工程上的策略可能会降低 LeetCode 上的测评性能

