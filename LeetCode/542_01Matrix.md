---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 542. 01 Matrix

> <https://leetcode.com/problems/01-matrix/description/>

## Description

Given a matrix consists of 0 and 1, find the distance of the nearest 0 for each cell.

The distance between two adjacent cells is 1.
**Example 1**: 
**Input**:
```
0 0 0
0 1 0
0 0 0
```
**Output**:
```
0 0 0
0 1 0
0 0 0
```

**Example 2**: 
**Input**:
```
0 0 0
0 1 0
1 1 1
```
**Output**:
```
0 0 0
0 1 0
1 2 1
```

**Note**:
- The number of elements of the given matrix will not exceed 10,000.
- There are at least one 0 in the given matrix.
- The cells are adjacent in only four directions: up, down, left and right.

## Solution

```python {class=line-numbers}
class Solution(object):
    def get_nexts(self, cur):
        r, c = cur
        if r-1>=0: yield (r-1, c)
        if r+1<self.rows: yield(r+1, c)
        if c-1>=0: yield (r, c-1)
        if c+1<self.columns: yield(r, c+1)
    def updateMatrix(self, matrix):
        """
        :type matrix: List[List[int]]
        :rtype: List[List[int]]
        """
        self.rows, self.columns = len(matrix), len(matrix[0])
        que = []
        for i in range(self.rows):
            for j in range(self.columns):
                if matrix[i][j]==0: que.append((i,j))
        steps = 1
        visited = set(que)
        while len(visited)!=self.rows*self.columns:
            tp_que = []
            for cur in que:
                for next_p in self.get_nexts(cur):
                    if next_p not in visited:
                        visited.add(next_p)
                        matrix[next_p[0]][next_p[1]] = steps
                        tp_que.append(next_p)
            que = tp_que
            steps += 1
        return matrix
```
