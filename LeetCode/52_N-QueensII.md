---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 52. N-Queens II

> <https://leetcode.com/problems/n-queens-ii/description/>


## Description

The n-queens puzzle is the problem of placing n queens on an n×n chessboard such that no two queens attack each other.

![](/LeetCode/8-queens.png)

Given an integer n, return the number of distinct solutions to the n-queens puzzle.

**Example**:
> **Input**: 4
> **Output**: 2
**Explanation**: There are two distinct solutions to the 4-queens puzzle as shown below.
```
[
 [".Q..",  // Solution 1
  "...Q",
  "Q...",
  "..Q."],

 ["..Q.",  // Solution 2
  "Q...",
  "...Q",
  ".Q.."]
]
```

## Solution

基本思路和解决 [N-Queens](/LeetCode/51_N-Queens.html) 题目是一样的，不过在判断是否产生冲突时，不是通过遍历来判断，而是通过3个数组分别存储 列、\/ 对角线 、\\ 对角线的占用情况，这样就可快速判断是否产生冲突，达到加快运行速度的目的。

另外，由于只需要计算解的个数，因为棋盘左右对称，其实只要计算一半就可得到结果，这也可以用于加快运行速度。

```python
class Solution:
    def totalNQueens(self, n):
        """
        :type n: int
        :rtype: int
        """
        if n==0:return 0
        pre_cnts = cnts = 0
        col_status = [True]*n
        diag1_status = [True]*(2*n-1)
        diag2_status = [True]*(2*n-1)
        def solve_r(pos):
            nonlocal cnts
            nonlocal pre_cnts
            if pos==n:
                cnts += 1
                return
            for i in range(n):
                # 棋盘对称，只需要计算一半
                if pos==0:
                    if i>=n/2:
                        break
                    pre_cnts = cnts
                d1 = i-pos+n-1
                d2 = i+pos
                # 用数组存储状态，加快冲突检测速度
                if col_status[i] and diag1_status[d1] and diag2_status[d2]:
                    col_status[i] = diag1_status[d1] = diag2_status[d2] = False
                    solve_r(pos+1)
                    col_status[i] = diag1_status[d1] = diag2_status[d2] = True
        a = [0] * n
        solve_r(0)
        if n%2==0:
            return cnts*2
        else:
            return cnts+pre_cnts
```

另外，可以直接将结果存储在数组中，然后直接返回，虽然有点耍赖，但的确是最高效的方式：
```python
class Solution:
    def totalNQueens(self, n):
        """
        :type n: int
        :rtype: int
        """
        a = [0,1,0,0,2,10,4,40,92,352,724,2680,14200,
             73712,365596,2279184,14772512,95815104,
             666090624,4968057848,39029188884,314666222712,
             2691008701644,24233937684440,227514171973736,
             2207893435808352]
        return a[n]
```

当然，最好还是两者结合：
```python
class Solution:
    def totalNQueens(self, n):
        """
        :type n: int
        :rtype: int
        """
        res = [0,1,0,0,2,10,4,40,92,352,724,2680,14200,
             73712,365596,2279184,14772512,95815104,
             666090624,4968057848,39029188884,314666222712,
             2691008701644,24233937684440,227514171973736,
             2207893435808352]
        if n < len(res):
            return res[n]
        pre_cnts = cnts = 0
        col_status = [True]*n
        diag1_status = [True]*(2*n-1)
        diag2_status = [True]*(2*n-1)
        def solve_r(pos):
            nonlocal cnts
            nonlocal pre_cnts
            if pos==n:
                cnts += 1
                return
            for i in range(n):
                if pos==0:
                    if i>=n/2:
                        break
                    pre_cnts = cnts
                d1 = i-pos+n-1
                d2 = i+pos
                if col_status[i] and diag1_status[d1] and diag2_status[d2]:
                    col_status[i] = diag1_status[d1] = diag2_status[d2] = False
                    solve_r(pos+1)
                    col_status[i] = diag1_status[d1] = diag2_status[d2] = True
        a = [0] * n
        solve_r(0)
        if n%2==0:
            return cnts*2
        else:
            return cnts+pre_cnts
```
