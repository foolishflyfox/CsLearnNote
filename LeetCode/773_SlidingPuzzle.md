---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 773. Sliding Puzzle

> <https://leetcode.com/problems/sliding-puzzle/description/>

## Description

On a 2x3 `board`, there are 5 tiles represented by the integers 1 through 5, and an empty square represented by 0.

A move consists of choosing `0` and a 4-directionally adjacent number and swapping it.

The state of the board is solved if and only if the `board` is `[[1,2,3],[4,5,0]]`.

Given a puzzle board, return the least number of moves required so that the state of the board is solved. If it is impossible for the state of the board to be solved, return `-1`.

**Examples:**

**Input:** board = `[[1,2,3],[4,0,5]]`
**Output:** `1`
**Explanation**: Swap the 0 and the 5 in one move.

**Input:** board = `[[1,2,3],[5,4,0]]`
**Output:** `-1`
**Explanation**: No number of moves will make the board solved.

**Input:** board = `[[4,1,2],[5,0,3]]`
**Output:** `5`
**Explanation**: 5 is the smallest number of moves that solves the board.
An example path:
After move 0: `[[4,1,2],[5,0,3]]`
After move 1: `[[4,1,2],[0,5,3]]`
After move 2: `[[0,1,2],[4,5,3]]`
After move 3: `[[1,0,2],[4,5,3]]`
After move 4: `[[1,2,0],[4,5,3]]`
After move 5: `[[1,2,3],[4,5,0]]`

**Input:** board = `[[3,2,4],[1,5,0]]`
**Output:** `14`


**Note:**
- `board` will be a 2 x 3 array as described above.
- `board[i][j]` will be a permutation of `[0, 1, 2, 3, 4, 5]`.

## Solution

```python {class=line-numbers}
class Solution:
    # 获取后继状态
    def get_next(self, cur_s):
        status = list(cur_s)
        i = status.index(0)
        for j in [-1, 1, -3, 3]:
            if not (0<=i+j<len(cur_s)): continue
            if (j==-1 and i+j==2) or (j==1 and i+j==3): 
                continue
            tp_s = status[:]
            tp_s[i], tp_s[i+j] = tp_s[i+j], tp_s[i]
            yield tuple(tp_s)
    def slidingPuzzle(self, board):
        """
        :type board: List[List[int]]
        :rtype: int
        """
        cur_s = tuple((i for i in board[0]+board[1]))
        # 记录访问过的状态
        status = set([cur_s])
        # 定义初始状态
        aim = (1,2,3,4,5,0)
        if cur_s==aim: return 0
        count = 1
        layer = [cur_s]
        next_layer = []
        while layer:
            cur_s = layer.pop()
            for next_s in self.get_next(cur_s):
                if next_s not in status:
                    if next_s==aim: return count
                    status.add(next_s)
                    next_layer.append(next_s)
            if len(layer)==0:
                layer = next_layer
                next_layer = []
                count += 1
        return -1
```
