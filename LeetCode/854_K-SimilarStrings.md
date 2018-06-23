---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 854. K-Similar Strings

> <https://leetcode.com/problems/k-similar-strings/description/>

## Description

Strings `A` and `B` are `K`-similar (for some non-negative integer `K`) if we can swap the positions of two letters in `A` exactly `K` times so that the resulting string equals `B`.

Given two anagrams `A` and `B`, return the smallest `K` for which `A` and `B` are `K`-similar.


**Example 1**:
**Input**: `A = "ab", B = "ba"`
**Output**: `1`

**Example 2**:
**Input**: `A = "abc", B = "bca"`
**Output**: `2`

**Example 3**:
**Input**: `A = "abac", B = "baca"`
**Output**: `2`

**Example 4**:
**Input**: `A = "aabc", B = "abca"`
**Output**: `2`

**Note**:

- `1 <= A.length == B.length <= 20`
- `A` and `B` contain only lowercase letters from the set `{'a', 'b', 'c', 'd', 'e', 'f'}`

## Solution

### Solution-1

使用纯动态规划：
```python {class=line-numbers}
class Solution:
    def kSimilarity(self, A, B):
        """
        :type A: str
        :type B: str
        :rtype: int
        """
        if A==B:return 0
        if A[0]==B[0]:
            return self.kSimilarity(A[1:], B[1:])
        k = len(A)
        for i in range(1, len(B)):
            if A[0] == B[i]:
                tp_B = B[1:i]+B[0]+B[i+1:]
                k = min(k, self.kSimilarity(A[1:], tp_B)+1)
        return k
```
测试案例如下：
```python
s = Solution()
A = "fffeaacbdbdafcfbbafb"
B = "abcbdfafffefabdbbafc"
%time s.kSimilarity(A, B)
```
结果为：
```python
CPU times: user 3.49 s, sys: 19.3 ms, total: 3.51 s
Wall time: 3.55 s
[output]: 10
```
在 LeetCode 上出现超时错误。

### Solution-2

优先交换那些经过一次互换之后，两个位置都之前正确的位置：
```python {class=line-numbers}
class Solution:
    def kSimilarity(self, A, B):
        """
        :type A: str
        :type B: str
        :rtype: int
        """
        if A==B:return 0
        if A[0]==B[0]:
            return self.kSimilarity(A[1:], B[1:])
        k = len(A)
        swap_indices = []
        for i in range(1, len(B)):
            if A[0] == B[i]:
                if A[i]==B[0]:
                    tp_B = B[1:i]+B[0]+B[i+1:]
                    return self.kSimilarity(A[1:], tp_B)+1
                else:
                    swap_indices.append(i)
        for i in swap_indices:
            tp_B = B[1:i]+B[0]+B[i+1:]
            k = min(k, self.kSimilarity(A[1:], tp_B)+1)
        return k
```
测试结果为：
```python
CPU times: user 2.71 ms, sys: 9 µs, total: 2.72 ms
Wall time: 2.74 ms
[output]: 10
```
测试速度比 Solution-1 快了 1000 多倍，在 LeetCode 上的测试时间为 128 ms，通过了测试。
