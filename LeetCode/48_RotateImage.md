---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 48. Rotate Image

> <https://leetcode.com/problems/rotate-image/description/>
## Description

You are given an `n x n` 2D matrix representing an image.

Rotate the image by 90 degrees (clockwise).

**Note** :

You have to rotate the image in-place, which means you have to modify the input 2D matrix directly. **DO NOT** allocate another 2D matrix and do the rotation.

**Example 1**:

```
Given input:
matrix = 
[
  [1,2,3],
  [4,5,6],
  [7,8,9]
],
```
rotate the input matrix in-place such that it becomes:
```
[
  [7,4,1],
  [8,5,2],
  [9,6,3]
]
```

**Example 2**:
```
Given input :
matrix =
[
  [ 5, 1, 9,11],
  [ 2, 4, 8,10],
  [13, 3, 6, 7],
  [15,14,12,16]
], 
```
rotate the input matrix in-place such that it becomes:
```
[
  [15,13, 2, 5],
  [14, 3, 4, 1],
  [12, 6, 8, 9],
  [16, 7,10,11]
]
```
## Solution

```python
class Solution(object):
    def rotate(self, matrix):
        """
        :type matrix: List[List[int]]
        :rtype: void Do not return anything, modify matrix in-place instead.
        """
        n = len(matrix)
        for i in range(n//2):
            for j in range(i, n-i-1):
                r1, c1 = i, j
                r2, c2 = j, n-i-1
                r3, c3 = n-i-1, n-1-j
                r4, c4 = n-1-j, i
                matrix[r1][c1], matrix[r2][c2], matrix[r3][c3], matrix[r4][c4] = (
                    matrix[r4][c4], matrix[r1][c1], matrix[r2][c2], matrix[r3][c3])
```
