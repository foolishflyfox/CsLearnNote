---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 240. Search a 2D Matrix II

> <https://leetcode.com/problems/search-a-2d-matrix-ii/description/>

## Description

Write an efficient algorithm that searches for a value in an m x n matrix. This matrix has the following properties:
- Integers in each row are sorted in ascending from left to right.
- Integers in each column are sorted in ascending from top to bottom.

**Example**:

Consider the following matrix:
```
[
  [1,   4,  7, 11, 15],
  [2,   5,  8, 12, 19],
  [3,   6,  9, 16, 22],
  [10, 13, 14, 17, 24],
  [18, 21, 23, 26, 30]
]
```
Given target = `5`, return `true`.

Given target = `20`, return `false`.

## Solution

###  完全递归的搜索

```python {class=line-numbers}
class Solution(object):
    def recursive_f(self, r_index, c_index):
        if (r_index>=self.rows or c_index>=self.columns or
           self.matrix[r_index][c_index]>self.target):
            return False
        if self.matrix[r_index][c_index]==self.target:
            return True
        return (self.recursive_f(r_index,c_index+1) or 
                self.recursive_f(r_index+1,c_index))
    def searchMatrix(self, matrix, target):
        """
        :type matrix: List[List[int]]
        :type target: int
        :rtype: bool
        """
        if len(matrix)==0 or len(matrix[0])==0: return False
        self.rows = len(matrix)
        self.columns = len(matrix[0])
        self.matrix = matrix
        self.target = target
        return self.recursive_f(0,0)
```
该算法会出现重复搜索，故会发生超时。

### 改进

```python {class=line-numbers}
class Solution(object):
    def recursive_f(self, r_index, c_index):
        if r_index>=self.rows or c_index>=self.columns:
            return False
        if self.matrix[r_index][c_index]==None: return False
        if self.matrix[r_index][c_index]==self.target:
            return True
        self.matrix[r_index][c_index] = None
        return (self.recursive_f(r_index,c_index+1) or 
                self.recursive_f(r_index+1,c_index))
    def searchMatrix(self, matrix, target):
        """
        :type matrix: List[List[int]]
        :type target: int
        :rtype: bool
        """
        if len(matrix)==0 or len(matrix[0])==0: return False
        self.rows = len(matrix)
        self.columns = len(matrix[0])
        self.matrix = matrix
        self.target = target
        return self.recursive_f(0,0)
```
