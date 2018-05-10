---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 525. Contiguous Array

> <https://leetcode.com/problems/contiguous-array/description/>

## Description


Given a binary array, find the maximum length of a contiguous subarray with equal number of 0 and 1.

**Example 1**:
>**Input**: [0,1]
**Output**: 2
**Explanation**: [0, 1] is the longest contiguous subarray with equal number of 0 and 1.

**Example 2**:
>**Input**: [0,1,0]
**Output**: 2
**Explanation**: [0, 1] (or [1, 0]) is a longest contiguous subarray with equal number of 0 and 1.

**Note**: The length of the given binary array will not exceed 50,000.

## Solution

### Solution-1

迭代寻找以指定的位置为开始节点的最长配对子串：
```python
class Solution:
    def findMaxLength(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        max_length = 0
        for i in range(len(nums)):
            if len(nums)-i <= max_length:
                break
            record = 0
            available_len = 0
            for j in range(i, len(nums)):
                k = 1 if nums[j] else -1
                if record*k < 0:
                    record += k
                    if record == 0:
                        available_len = j-i+1
                else:
                    record += k
            if available_len > max_length:
                max_length = available_len
        return max_length
```
时间复杂度为 $O(n^2)$ 在 LeetCode 上运行时超时。

### Solution-2

求和，和相等的两个索引之差即为匹配子串的长度：
```python
class Solution:
    def findMaxLength(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        record_dict = {0:-1}
        cur_sum = 0
        max_len = 0
        for i in range(len(nums)):
            cur_sum += 1 if nums[i] else -1
            if cur_sum not in record_dict:
                record_dict[cur_sum] = i
            elif i - record_dict[cur_sum] > max_len:
                max_len = i-record_dict[cur_sum]
        return max_len
```
该算法的时间复杂度仅仅为 $O(n)$。

## 总结

对于寻找子串的问题，如果希望到达 $O(n)$ 的时间复杂度，必定是通过依次对迭代器对象的前m个数进行某种操作，如该问题中的 求和，通过观察结果的特性来从中找出子串。
