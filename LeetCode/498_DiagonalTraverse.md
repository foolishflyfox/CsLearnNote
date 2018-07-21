---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 498. Diagonal Traverse

> <https://leetcode.com/problems/diagonal-traverse/description/>

## Description

Given a matrix of M x N elements (M rows, N columns), return all elements of the matrix in diagonal order as shown in the below image.

**Example**:
**Input**:
```
[
 [ 1, 2, 3 ],
 [ 4, 5, 6 ],
 [ 7, 8, 9 ]
]
```
**Output**:  `[1,2,4,7,5,3,6,8,9]`
**Explanation**:
![](/LeetCode/diagonal_traverse.png)

**Note**:
- The total number of elements of the given matrix will not exceed 10,000.

## Solution

```python {class=line-numbers}
class Solution(object):
    def findDiagonalOrder(self, matrix):
        """
        :type matrix: List[List[int]]
        :rtype: List[int]
        """
        if len(matrix)==0: return []
        m, n = len(matrix), len(matrix[0])
        direction = 1
        ret = []
        for i in range(m+n-1):
            if direction==1: p1, p2 = max(0,i-m+1), min(i, n-1)+1
            else: p2, p1 = max(0,i-m+1)-1, min(i, n-1)
            for j in range(p1, p2, direction):
                ret.append(matrix[i-j][j])
            direction *= -1
        return ret
```


