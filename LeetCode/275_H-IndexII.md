---
export_on_save:
 html: true
---

@import "/blog_head.md"

#  275. H-Index II

> <https://leetcode.com/problems/h-index-ii/description/>

## Description

Given an array of citations **sorted in ascending order** (each citation is a non-negative integer) of a researcher, write a function to compute the researcher's h-index.

According to the definition of h-index on Wikipedia: "A scientist has index h if h of his/her N papers have **at least h** citations each, and the other N − h papers have no more than h citations each."

**Example**:

**Input**: citations = `[0,1,3,5,6]`
**Output**: `3` 
**Explanation**: [0,1,3,5,6] means the researcher has 5 papers in total and each of them had received 0, 1, 3, 5, 6 citations respectively. Since the researcher has 3 papers with at least 3 citations each and the remaining  two with no more than 3 citations each, her h-index is 3.

**Note:**

If there are several possible values for h, the maximum one is taken as the h-index.

**Follow up**:

- This is a follow up problem to H-Index, where citations is now guaranteed to be sorted in ascending order.
- Could you solve it in logarithmic time complexity?

## Solution

### Solution-1

最多的引用次数为 `len(citations)` 次，开辟一个 `len(citations)+1` 长度的数组，用该数组进行计数：
```python {class=line-numbers}
class Solution(object):
    def hIndex(self, citations):
        """
        :type citations: List[int]
        :rtype: int
        """
        import numpy as np
        record = np.array([0]*(len(citations)+1))
        for i in citations:
            record[:i+1] += 1
        for i in range(len(citations)+1):
            if i>record[i]:
                return i-1
        else:
            return i
```
结果：numpy 模块在LeetCode中不能使用；

### Solution-2

将 numpy 模块的加操作（上面代码的第10行） 换成 for 循环：
```python {class=line-numbers}
class Solution(object):
    def hIndex(self, citations):
        """
        :type citations: List[int]
        :rtype: int
        """
        import numpy as np
        length = len(citations)
        record = [0]*(length+1)
        for i in citations:
            for j in range(min(i+1, length+1)):
                record[j] += 1
        for i in range(len(citations)+1):
            if i>record[i]:
                return i-1
        else:
            return i
```
结果：超时；计算的复杂度为 $O(n^2+n)=O(n^2)$

### Solution-3

上面两个代码都没有将数组元素递增的特性用起来，下面将该特性用起来：
```python {class=line-numbers}
class Solution(object):
    def hIndex(self, citations):
        """
        :type citations: List[int]
        :rtype: int
        """
        ret = 0
        length = len(citations)
        for i in range(length):
            if citations[i] >= length-i:
                ret = max(ret, length-i)
        return ret
```
上面代码在 LeetCode 上的测试时间是 250ms 左右；时间复杂度为 $O(n)$。

如果 `citation` 是无序状态，那先用 快排 等时间复杂度为 $O(n\log n)$ 的排序算法进行排序，时间复杂度为 $O(n\log n+n)=O(n\log n)$

### Solution-4

下面用二分查找再度加速：
```python {class=line-numbers}
class Solution(object):
    def hIndex(self, citations):
        """
        :type citations: List[int]
        :rtype: int
        """
        ret = 0
        length = len(citations)
        right = length-1
        left = 0
        while left <= right:
            i = (left+right)//2
            if citations[i] >= length-i:
                ret = max(ret, length-i)
                right = i-1
            else:
                ret = max(ret, citations[i])
                left = i+1
        return ret
```
上面代码在 LeetCode 上的测试时间是 50ms 左右；时间复杂度为 $O(\log n)$，比上面的 $O(n)$ 复杂度快很多。
