---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 797. All Paths From Source to Target

> <https://leetcode.com/problems/all-paths-from-source-to-target/description/>

## Description

Given a directed, acyclic graph of `N` nodes.  Find all possible paths from node `0` to node `N-1`, and return them in any order.

The graph is given as follows:  the nodes are 0, 1, ..., graph.length - 1.  graph[i] is a list of all nodes j for which the edge (i, j) exists.

**Example**:

> **Input**: `[ [1,2], [3], [3], [] ] `
> **Output**: `[ [0,1,3],[0,2,3] ]` 
**Explanation**: The graph looks like this:
```
0--->1
|    |
v    v
2--->3
```
There are two paths: 0 -> 1 -> 3 and 0 -> 2 -> 3.

**Note**:

- The number of nodes in the graph will be in the range `[2, 15]`.
- You can print different paths in any order, but you should keep the order of nodes inside one path.

## Solution

```python
class Solution:
    def allPathsSourceTarget(self, graph):
        """
        :type graph: List[List[int]]
        :rtype: List[List[int]]
        """
        from collections import deque
        nodes_num = len(graph)
        q = deque([[0]])
        routes = []
        while len(q):
            cur_path = q.popleft()
            if cur_path[-1]==nodes_num-1:
                routes.append(cur_path)
                continue
            for next_n in graph[cur_path[-1]]:
                q.append(cur_path[:]+[next_n])
        return routes
```

