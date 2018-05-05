---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 417. Pacific Atlantic Water Flow

> <https://leetcode.com/problems/pacific-atlantic-water-flow/description/>

## Discription

Given an $m\times n$ matrix of non-negative integers representing the height of each unit cell in a continent, the "Pacific ocean" touches the left and top edges of the matrix and the "Atlantic ocean" touches the right and bottom edges.

Water can only flow in four directions (up, down, left, or right) from a cell to another one with height equal or lower.

Find the list of grid coordinates where water can flow to both the Pacific and Atlantic ocean.

**Note**:
1. The order of returned grid coordinates does not matter.
1. Both m and n are less than 150.

**Example**:
```
Given the following 5x5 matrix:

  Pacific ~   ~   ~   ~   ~ 
       ~  1   2   2   3  (5) *
       ~  3   2   3  (4) (4) *
       ~  2   4  (5)  3   1  *
       ~ (6) (7)  1   4   5  *
       ~ (5)  1   1   2   4  *
          *   *   *   *   * Atlantic

Return:

[[0, 4], [1, 3], [1, 4], [2, 2], [3, 0], [3, 1], [4, 0]] (positions with parentheses in above matrix).
```

## Solution
```python
class Solution:
    def pacificAtlantic(self, matrix):
        """
        :type matrix: List[List[int]]
        :rtype: List[List[int]]
        """
        n = len(matrix)
        if n==0:
            return []
        m = len(matrix[0])
        flags = [[0 for j in range(m)] for i in range(n)]
        pt_queue = []
        for i in range(n):
            for j in range(m):
                pt_queue.append([i, j])
                is_pop = True
                if i==0 or j ==0:
                    flags[i][j] |= 1
                    is_pop = False
                if i==n-1 or j==m-1:
                    flags[i][j] |= 2
                    is_pop = False
                if is_pop:
                    pt_queue.pop()
        while len(pt_queue):
            pt = pt_queue[0]
            del pt_queue[0]
            for i in ((-1,0),(1,0),(0,-1),(0,1)):
                tp_pt = [pt[0]+i[0], pt[1]+i[1]]
                if(0<=tp_pt[0]<n and 0<=tp_pt[1]<m and
                matrix[tp_pt[0]][tp_pt[1]] >= matrix[pt[0]][pt[1]] and
                flags[tp_pt[0]][tp_pt[1]]!=3 and
                flags[tp_pt[0]][tp_pt[1]]^flags[pt[0]][pt[1]]!=0):
                    flags[tp_pt[0]][tp_pt[1]] |= flags[pt[0]][pt[1]]
                    pt_queue.append(tp_pt)
        return [[i,j] for i in range(n) for j in range(m)
               if flags[i][j]==3]
```
