---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 813. Largest Sum of Averages

> <https://leetcode.com/problems/largest-sum-of-averages/description/>

## Description

We partition a row of numbers `A` into at most `K` adjacent (non-empty) groups, then our score is the sum of the average of each group. What is the largest score we can achieve?

Note that our partition must use every number in A, and that scores are not necessarily integers.

**Example**:
**Input**: 
A = [9,1,2,3,9]
K = 3
**Output**: 20
**Explanation**: 
The best choice is to partition A into `[9], [1, 2, 3], [9]`. The answer is `9 + (1 + 2 + 3) / 3 + 9 = 20`.
We could have also partitioned A into `[9, 1], [2], [3, 9]`, for example.
That partition would lead to a score of `5 + 2 + 6 = 13`, which is worse.
 

**Note**:
- `1 <= A.length <= 100`.
- `1 <= A[i] <= 10000`.
- `1 <= K <= A.length`.
- Answers within `10^-6` of the correct answer will be accepted as correct.


## Solution

### Solution-1

```python {class=line-numbers}
class Solution(object):
    def largestSumOfAverages(self, A, K):
        """
        :type A: List[int]
        :type K: int
        :rtype: float
        """
        if K>len(A): return -float('inf')
        if len(A)==K: return sum(A)
        if K==1: return 1.0*sum(A)/len(A)
        ret = -float('inf')
        for i in range(1, len(A)):
            cur_ret = 1.0*sum(A[:i])/(i)+self.largestSumOfAverages(A[i:], K-1)
            if cur_ret==-float('inf'):
                break
            ret = max(ret, cur_ret)
        return ret
```
在输入为：
```
A = [4663,3020,7789,1627,9668,1356,4207,1133,8765,4649,205,6455,8864,3554,3916,5925,
3995,4540,3487,5444,8259,8802,6777,7306,989,4958,2921,8155,4922,2469,6923,776,9777,
1796,708,786,3158,7369,8715,2136,2510,3739,6411,7996,6211,8282,4805,236,1489,7698]
27
```
时发生网络超时；

### Solution-2
从树搜索改为图搜索：
```python {class=line-numbers}
class Solution(object):
    def calc_recursive(self, A, K):
        if (tuple(A), K) in self.record:
            return self.record[(tuple(A), K)]
        if K>len(A): return -float('inf')
        if len(A)==K: return sum(A)
        if K==1: return 1.0*sum(A)/len(A)
        ret = -float('inf')
        for i in range(1, len(A)):
            cur_ret = 1.0*sum(A[:i])/(i)+self.calc_recursive(A[i:], K-1)
            if cur_ret==-float('inf'):
                break
            ret = max(ret, cur_ret)
        self.record[(tuple(A), K)] = ret
        return ret
    def largestSumOfAverages(self, A, K):
        """
        :type A: List[int]
        :type K: int
        :rtype: float
        """
        self.record = dict()
        return self.calc_recursive(A, K)
```
上述代码通过测试。

