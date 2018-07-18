---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 63. Unique Paths II

> <https://leetcode.com/problems/unique-paths-ii/description/>

## Description

A robot is located at the top-left corner of a m x n grid (marked 'Start' in the diagram below).

The robot can only move either down or right at any point in time. The robot is trying to reach the bottom-right corner of the grid (marked 'Finish' in the diagram below).

Now consider if some obstacles are added to the grids. How many unique paths would there be?

![](/LeetCode/robot_maze.png)

An obstacle and empty space is marked as `1` and `0` respectively in the grid.

**Note**: m and n will be at most 100.

**Example 1**:

**Input**:
```
[
  [0,0,0],
  [0,1,0],
  [0,0,0]
]
```
**Output**: 2
**Explanation**:
There is one obstacle in the middle of the 3x3 grid above. There are two ways to reach the bottom-right corner:
1. Right -> Right -> Down -> Down
2. Down -> Down -> Right -> Right

## Solution

```python {class=line-numbers}
class Solution(object):
    def get_paths(self, row_p, col_p):
        if (row_p >= self.rows or col_p >= self.cols 
            or self.obstacleGrid[row_p][col_p]==1): 
            return 0
        if row_p==self.rows-1 and col_p==self.cols-1:
            return 1
        if self.record[row_p][col_p]>=0: 
            return self.record[row_p][col_p]
        cur_paths = (self.get_paths(row_p, col_p+1) + 
                     self.get_paths(row_p+1, col_p))
        self.record[row_p][col_p] = cur_paths
        return cur_paths
    def uniquePathsWithObstacles(self, obstacleGrid):
        """
        :type obstacleGrid: List[List[int]]
        :rtype: int
        """
        self.obstacleGrid = obstacleGrid
        self.rows = len(obstacleGrid)
        self.cols = len(obstacleGrid[0])
        self.record = [[-1]*self.cols for i in range(self.rows)]
        return self.get_paths(0, 0)
```

