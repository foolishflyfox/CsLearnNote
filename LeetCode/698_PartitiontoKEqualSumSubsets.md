---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 698. Partition to K Equal Sum Subsets

> <https://leetcode.com/problems/partition-to-k-equal-sum-subsets/description/>

## Description

Given an array of integers `nums` and a positive integer `k`, find whether it's possible to divide this array into `k` non-empty subsets whose sums are all equal.

**Example 1**:
> **Input**: nums = [4, 3, 2, 3, 5, 2, 1], k = 4
**Output**: True
**Explanation**: It's possible to divide it into 4 subsets (5), (1, 4), (2,3), (2,3) with equal sums.

**Note**:

- `1 <= k <= len(nums) <= 16`
- `0 < nums[i] < 10000`

## Solutions

### Dynamic Programming
该解决方案使用 递归 + 动态规划。其中动态规划的方案通过组合函数 `itertools.combinations` 生成。
```python
class Solution:
    def canPartitionKSubsets(self, nums, k):
        """
        :type nums: List[int]
        :type k: int
        :rtype: bool
        """
        from itertools import combinations
        def DP(a, mean_v):
            if len(a)==0:
                return True
            if a[-1]>mean_v:
                return False
            if a[-1]==mean_v:
                return DP(a[:-1], mean_v)
            request_v = mean_v-a[-1]
            index_max = 0
            while index_max<len(a) and a[index_max]<=request_v:
                index_max += 1
            index_max -= 1
            if index_max < 0:
                return False
            for i in range(1, index_max+2):
                for j in combinations(range(index_max+1), i):
                    sum_v = 0
                    for k in j:
                        sum_v += a[k]
                    if sum_v == request_v:
                        tp_a = a[:]
                        for k in j[::-1]:
                            del tp_a[k]
                        if DP(tp_a[:-1], mean_v):
                            return True
            return False
            
        nums_sum = sum(nums)
        if nums_sum%k!=0:
            return False
        mean_v = nums_sum//k
        nums.sort()
        return DP(nums, mean_v)
```

### DFS (递归)

```python
class Solution(object):
    def canPartitionKSubsets(self, nums, k):
        if k==1: return True

        self.n=len(nums)
        if k>self.n: return False

        total=sum(nums)
        if total%k: return False

        self.target=total/k
        visit=[0]*self.n

        nums.sort(reverse=True)
        def dfs(k,ind,sum,cnt):
            if k==1: return True
            if sum==self.target and cnt>0:
                return dfs(k-1,0,0,0)
            for i in range(ind,self.n):
                if not visit[i] and sum+nums[i]<=self.target:
                    visit[i]=1
                    if dfs(k,i+1,sum+nums[i],cnt+1): 
                        return True
                    visit[i]=0
            return False

        return dfs(k,0,0,0)
```

