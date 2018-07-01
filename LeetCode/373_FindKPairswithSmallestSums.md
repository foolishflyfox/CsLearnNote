---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 373. Find K Pairs with Smallest Sums

> <https://leetcode.com/problems/find-k-pairs-with-smallest-sums/description/>

## Description

You are given two integer arrays **nums1** and **nums2** sorted in ascending order and an integer **k**.

Define a pair **(u,v)** which consists of one element from the first array and one element from the second array.

Find the k pairs **(u1,v1),(u2,v2) ...(uk,vk)** with the smallest sums.

**Example 1**:
```
Given nums1 = [1,7,11], nums2 = [2,4,6],  k = 3

Return: [1,2],[1,4],[1,6]

The first 3 pairs are returned from the sequence:
[1,2],[1,4],[1,6],[7,2],[7,4],[11,2],[7,6],[11,4],[11,6]
```
**Example 2**:
```
Given nums1 = [1,1,2], nums2 = [1,2,3],  k = 2

Return: [1,1],[1,1]

The first 2 pairs are returned from the sequence:
[1,1],[1,1],[1,2],[2,1],[1,2],[2,2],[1,3],[1,3],[2,3]
```

**Example 3**:
```
Given nums1 = [1,2], nums2 = [3],  k = 3 

Return: [1,3],[2,3]

All possible pairs are returned from the sequence:
[1,3],[2,3]
```

## Solution

### Solution-1

产生所有的组合并进行排序：
```python {class=line-numbers}
class Solution(object):
    def kSmallestPairs(self, nums1, nums2, k):

        """
        :type nums1: List[int]
        :type nums2: List[int]
        :type k: int
        :rtype: List[List[int]]
        """
        return sorted([(i,j) for i in nums1 for j in nums2], 
                      key=lambda x:x[0]+x[1])[:k]
```
缺点：需要大量的内存，时间复杂度也非常高。

### Solution-2

堆+搜索
```python {class=line-numbers}
import heapq

class Solution(object):
    def kSmallestPairs(self, nums1, nums2, k):

        """
        :type nums1: List[int]
        :type nums2: List[int]
        :type k: int
        :rtype: List[List[int]]
        """
        if len(nums1)*len(nums2)<=k:
            return sorted([(i,j) for i in nums1 for j in nums2],
                          key=lambda x:x[0]+x[1])[:k]
        l1, l2 = len(nums1), len(nums2)
        visited = {(0,0)}
        ret = []
        pairs = [(nums1[0]+nums2[0],(0,0))]
        while len(ret)<k and len(pairs):
            _, (p1, p2) = heapq.heappop(pairs)
            ret.append((nums1[p1], nums2[p2]))
            for x1,x2 in [(p1-1, p2),(p1+1,p2),(p1,p2-1),(p1,p2+1)]:
                if (x1,x2) not in visited and 0<=x1<l1 and 0<=x2<l2:
                    heapq.heappush(pairs, (nums1[x1]+nums2[x2], (x1, x2)))
                    visited.add((x1,x2))
        return ret
```
