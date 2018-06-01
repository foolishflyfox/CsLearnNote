---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 51. N-Queens

> <https://leetcode.com/problems/n-queens/description/>

## Description

The n-queens puzzle is the problem of placing n queens on an n×n chessboard such that no two queens attack each other.

![](/LeetCode/8-queens.png)


Given an integer n, return all distinct solutions to the n-queens puzzle.

Each solution contains a distinct board configuration of the n-queens' placement, where `'Q'` and `'.'` both indicate a queen and an empty space respectively.

**Example:**

**Input:** 4
**Output:**
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
**Explanation:** There exist two distinct solutions to the 4-queens puzzle as shown above.

## Solution

```python
class Solution:
    def solveNQueens(self, n):
        """
        :type n: int
        :rtype: List[List[str]]
        """
        def solve_r(pos):
            if pos==n:
                one_s = []
                for p in a:
                    row = ['.'] * n
                    row[p] = 'Q'
                    one_s.append(''.join(row))
                rets.append(one_s)
                return
            for i in range(n):
                for p in range(pos):
                    if a[p]==i or abs(pos-p)==abs(a[p]-i):
                        break
                else:
                    a[pos] = i
                    solve_r(pos+1)
        a = [0] * n
        rets = []
        solve_r(0)
        return rets
```

