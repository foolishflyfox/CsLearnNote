---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 786. K-th Smallest Prime Fraction

> <https://leetcode.com/problems/k-th-smallest-prime-fraction/description/>

## Description

A sorted list `A` contains 1, plus some number of primes.  Then, for every p < q in the list, we consider the fraction p/q.

What is the `K`-th smallest fraction considered?  Return your answer as an array of ints, where `answer[0] = p` and `answer[1] = q`.

**Examples**:
**Input**: `A = [1, 2, 3, 5], K = 3`
**Output**: `[2, 5]`
**Explanation**:
The fractions to be considered in sorted order are: `1/5, 1/3, 2/5, 1/2, 3/5, 2/3`.
The third fraction is `2/5`.

**Input**: `A = [1, 7], K = 1`
**Output**: `[1, 7]`

**Note**:
- `A` will have length between `2` and `2000`.
- Each `A[i]` will be between `1` and `30000`.
- `K` will be between `1` and `A.length * (A.length - 1) / 2`.

## Solution

### Solution-1

```python {class=line-numbers}
class Solution(object):
    def kthSmallestPrimeFraction(self, A, K):
        """
        :type A: List[int]
        :type K: int
        :rtype: List[int]
        """
        import bisect
        ret = (A[0], A[-1])
        if K==1: return list(ret)
        primes_dict = {v:i for i,v in enumerate(A)}
        visited = {ret}
        record = [(-1.0*A[0]/A[-1], A[0], A[-1])]
        for i in range(1, K):
            _, a, b = record.pop()
            ia, ib = primes_dict[a], primes_dict[b]
            if ia+1<ib:
                for new_a, new_b in [(A[ia+1], b), (a, A[ib-1])]:
                    if (new_a, new_b) not in visited:
                        visited.add((new_a, new_b))
                        bisect.insort(record, (-1.0*new_a/new_b, new_a, new_b))
        return [record[-1][1], record[-1][2]]
```
上面代码由于持续记录访问过的节点，或占用大量的空间，下面是改进，能够及时清理 `visited` 变量中的元素。

### Solution-2

```python {class=line-numbers}
class Solution(object):
    def kthSmallestPrimeFraction(self, A, K):
        """
        :type A: List[int]
        :type K: int
        :rtype: List[int]
        """
        import bisect
        ret = (A[0], A[-1])
        if K==1: return list(ret)
        primes_dict = {v:i for i,v in enumerate(A)}
        visited = {ret}
        record = [(-1.0*A[0]/A[-1], A[0], A[-1])]
        for i in range(1, K):
            _, a, b = record.pop()
            ia, ib = primes_dict[a], primes_dict[b]
            if ia-1>0 and (A[ia-1], A[ib]) in visited: 
                visited.remove((A[ia-1], A[ib]))
            if ib+1<len(A) and (A[ia], A[ib+1]) in visited: 
                visited.remove((A[ia], A[ib+1]))
            if ia+1<ib:
                for new_a, new_b in [(A[ia+1], b), (a, A[ib-1])]:
                    if (new_a, new_b) not in visited:
                        visited.add((new_a, new_b))
                        bisect.insort(record, (-1.0*new_a/new_b, new_a, new_b))
        print(len(visited))
        return [record[-1][1], record[-1][2]]
```
上面的代码通过了测试，不过 LeetCode 上显示花费 8940ms。

### Solution-3

```python {class=line-numbers}
import bisect as bi
class Solution(object):
    def kthSmallestPrimeFraction(self, A, K):
        """
        :type A: List[int]
        :type K: int
        :rtype: List[int]
        """         
        l, r, n = 0.0, 1.0, len(A)
        while True:
            m = (l+r)/2
            border = [bi.bisect(A, A[i]*m) for i in xrange(n)]            
            cur = sum(border)
            if cur > K:
                r = m
            elif cur < K:
                l = m
            else:
                return max([[A[j-1], A[i]] for i, j in enumerate(border) if j > 0], 
                    key=lambda x: float(x[0])/x[1])
```
