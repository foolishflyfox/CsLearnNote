---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 64. Minimum Path Sum

## Description

Given a m x n grid filled with non-negative numbers, find a path from top left to bottom right which minimizes the sum of all numbers along its path.

**Note**: You can only move either down or right at any point in time.

**Example**:

**Input**:
```
[
  [1,3,1],
  [1,5,1],
  [4,2,1]
]
```
**Output**: 7
**Explanation**: Because the path 1→3→1→1→1 minimizes the sum.

## Solutions

### 一致代价搜索

```python {class="line-numbers"}
class Solution:
    def minPathSum(self, grid):
        """
        :type grid: List[List[int]]
        :rtype: int
        """
        from collections import namedtuple,deque
        import bisect
        if len(grid)==0:
            return 0
        Node = namedtuple('Node', 'cost pos')
        node_q = deque([Node(grid[0][0], (0,0))])
        right, bottom = len(grid[0])-1, len(grid)-1
        visited = set((0,0))
        while len(node_q):
            cur_node = node_q.popleft()
            if cur_node.pos==(bottom, right):
                break
            next_poses = []
            if cur_node.pos[0]<bottom:
                nb, nr = cur_node.pos
                nb += 1
                next_poses.append((nb, nr))
            if cur_node.pos[1]<right:
                nb, nr = cur_node.pos
                nr += 1
                next_poses.append((nb, nr))
            for pos in next_poses:
                if pos in visited : continue
                visited.add(pos)
                n = Node(cur_node.cost+grid[pos[0]][pos[1]],pos)
                node_q.insert(bisect.bisect(node_q, n), n)
        return cur_node.cost
```

通过 LeetCode 测试，不过用时 183ms，只超越了 1.3% 。

### 递归

```python {class="line-numbers"}
class Solution:
    def minPathSum(self, grid):
        """
        :type grid: List[List[int]]
        :rtype: int
        """
        def DP(cur_pos, grid):
            bottom, right = len(grid)-1, len(grid[0])-1
            if cur_pos[0]>bottom or cur_pos[1]>right:
                return float('inf')
            cur_cost = grid[cur_pos[0]][cur_pos[1]]
            if cur_pos==(bottom, right):
                return cur_cost
            row,col = cur_pos
            return cur_cost+min(DP((row+1,col), grid), 
                                DP((row,col+1), grid))
        
        if len(grid)==0:return 0
        return DP((0,0), grid)
```

思路清晰，没有通过时间限制。

### 迪杰斯特拉算法

采用类似迪杰斯特拉算法的思路。
```python {class="line-numbers"}
class Solution:
    def minPathSum(self, grid):
        """
        :type grid: List[List[int]]
        :rtype: int
        """
        if len(grid)==0:return 0
        for j in range(1, len(grid[0])):
            grid[0][j] += grid[0][j-1]
        for i in range(1, len(grid)):
            grid[i][0] += grid[i-1][0]
            for j in range(1, len(grid[i])):
                grid[i][j] += min(grid[i-1][j],grid[i][j-1])
        return grid[-1][-1]
```
优点：更加简洁，速度更快，用时为 52ms，击败 99.39 的提交。
