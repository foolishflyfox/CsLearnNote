---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 221. Maximal Square

> <https://leetcode.com/problems/maximal-square/description/>

## Description

Given a 2D binary matrix filled with 0's and 1's, find the largest square containing only 1's and return its area.

**Example**:

**Input**: 

1 0 1 0 0
1 0 <font color='red'>1 1</font> 1
1 1 <font color='red'>1 1</font> 1
1 0 0 1 0

**Output**: 4

## Solution

### Solution - 1

使用 numpy 库，思路简单，缺点是费时：
```python
class Solution:
    def maximalSquare(self, matrix):
        """
        :type matrix: List[List[str]]
        :rtype: int
        """
        import numpy as np
        npa = np.array(matrix).astype(int)
        del matrix
        maximal = 0
        if len(npa.shape)<2:
            return 0
        m, n = npa.shape
        for i in range(m):
            if i+maximal>=m:
                break
            j = 0
            while j < n:
                if j+maximal>=n or i+maximal>=m:
                    break
                if npa[i,j]==0:
                    j += 1
                    continue
                if (npa[i:(i+maximal+1),j:(j+maximal+1)]==0).any():
                    j += 1
                else:
                    maximal += 1
        return maximal*maximal
```

### Solution - 2

利用一定的数学知识，优点是速度快：
```python
class Solution:
    def maximalSquare(self, matrix):
        """
        :type matrix: List[List[str]]
        :rtype: int
        """
        if len(matrix)==0 or len(matrix[0])==0:
            return 0
        m, n = len(matrix), len(matrix[0])
        mat_i = [[int(j) for j in row] for row in matrix]
        del matrix
        ret_max = int(sum(mat_i[0])>0)
        for i in range(1,m):
            if mat_i[i][0] > ret_max:
                ret_max = mat_i[i][0]
            for j in range(1, n):
                if mat_i[i][j]:
                    mat_i[i][j] = min(mat_i[i][j-1], mat_i[i-1][j],
                                     mat_i[i-1][j-1])+1
                    if mat_i[i][j]>ret_max:
                        ret_max = mat_i[i][j]
        return ret_max*ret_max
```

