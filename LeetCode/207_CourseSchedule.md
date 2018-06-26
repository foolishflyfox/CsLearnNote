---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 207. Course Schedule

> <https://leetcode.com/problems/course-schedule/description/>

## Description

There are a total of n courses you have to take, labeled from `0` to `n-1`.

Some courses may have prerequisites, for example to take course 0 you have to first take course 1, which is expressed as a pair: `[0,1]`

Given the total number of courses and a list of prerequisite **pairs**, is it possible for you to finish all courses?

**Example 1**:
**Input**: `2, [[1,0]] `
**Output**: `true`
**Explanation**: There are a total of 2 courses to take. To take course 1 you should have finished course 0. So it is possible.

**Example 2**:
**Input**: `2, [[1,0],[0,1]]`
**Output**: `false`
**Explanation**: There are a total of 2 courses to take. To take course 1 you should have finished course 0, and to take course 0 you should also have finished course 1. So it is impossible.

**Note**:
- The input prerequisites is a graph represented by a list of edges, not adjacency matrices. Read more about how a graph is represented.
- You may assume that there are no duplicate edges in the input prerequisites.

## Solution

### Solution-1

思路：用 dict 构建一个图，检测是否有环：
```python {class=line-numbers}
class MultiNodeTree:
    def __init__(self, x):
        self.val = x
        self.children = []
class Solution:
    def canFinish(self, numCourses, prerequisites):
        """
        :type numCourses: int
        :type prerequisites: List[List[int]]
        :rtype: bool
        """
        from collections import deque
        if len(prerequisites)==0:return True
        record = dict()
        roots = set()
        unroots = set()
        for cur, pre in prerequisites:
            if cur not in record:
                record[cur] = MultiNodeTree(cur)
            if pre not in record:
                record[pre] = MultiNodeTree(pre)
            record[pre].children.append(record[cur])
            if pre not in unroots: roots.add(pre)
            if cur in roots: roots.remove(cur)
            unroots.add(cur)
        if len(roots)==0:return False
        for tree_val in roots:
            tree = record[tree_val]
            visited = set([tree_val])
            que = deque([tree])
            while que:
                cur_node = que.popleft()
                for child in cur_node.children:
                    if child.val in visited:
                        return False
                    que.append(child)
                    visited.add(child.val)
        return True
```
结果：在输入为 `3, [[0,1],[0,2],[1,2]]` 时出错，因为上述代码是对 **无向图** 检测是否含有环。

### Solution-2

将对无向图的检查修改为对有向图的检测：
```python {class=line-numbers}
class MultiNodeTree:
    def __init__(self, x):
        self.val = x
        self.children = []
class Solution:
    def canFinish(self, numCourses, prerequisites):
        """
        :type numCourses: int
        :type prerequisites: List[List[int]]
        :rtype: bool
        """
        from collections import deque
        if len(prerequisites)==0:return True
        record = dict()
        roots = set()
        unroots = set()
        for cur, pre in prerequisites:
            if cur not in record:
                record[cur] = MultiNodeTree(cur)
            if pre not in record:
                record[pre] = MultiNodeTree(pre)
            record[pre].children.append(record[cur])
            if pre not in unroots: roots.add(pre)
            if cur in roots: roots.remove(cur)
            unroots.add(cur)
        if len(roots)==0:return False
        for tree_val in roots:
            tree = record[tree_val]
            visited = set([tree_val])
            que = deque([tree])
            while que:
                cur_node = que.popleft()
                for child in cur_node.children:
                    if child.val in visited:
                        tp_visited = set([child.val])
                        tp_que = deque([child])
                        while tp_que:
                            tp_node = tp_que.popleft()
                            for tp_child in tp_node.children:
                                if tp_child.val in tp_visited:continue
                                if tp_child.val==cur_node.val:return False
                                tp_que.append(tp_child)
                                tp_visited.add(tp_child.val)
                    que.append(child)
                    visited.add(child.val)
        return True
```
结果：上述代码在输入为 `8, [[1,0],[2,6],[1,7],[5,1],[6,4],[7,0],[0,5]]` 出错，因为在构建过程中，将 `roots` 中有循化图的根删除了；

### Solution-3

修正 Solution-2 的 bug：
```python {class=line-numbers}
class MultiNodeTree:
    def __init__(self, x):
        self.val = x
        self.children = []
class Solution:
    def canFinish(self, numCourses, prerequisites):
        """
        :type numCourses: int
        :type prerequisites: List[List[int]]
        :rtype: bool
        """
        from collections import deque
        if len(prerequisites)==0:return True
        record = dict()
        node_vals = set()
        for cur, pre in prerequisites:
            if cur not in record:
                record[cur] = MultiNodeTree(cur)
            if pre not in record:
                record[pre] = MultiNodeTree(pre)
            record[pre].children.append(record[cur])
            node_vals.add(cur)
            node_vals.add(pre)
        visited = set()
        for tree_val in node_vals:
            if tree_val in visited:continue
            tree = record[tree_val]
            visited.add(tree_val)
            que = deque([tree])
            while que:
                cur_node = que.popleft()
                for child in cur_node.children:
                    if child.val in visited:
                        tp_visited = set([child.val])
                        tp_que = deque([child])
                        while tp_que:
                            tp_node = tp_que.popleft()
                            for tp_child in tp_node.children:
                                if tp_child.val in tp_visited:continue
                                if tp_child.val==cur_node.val:return False
                                tp_que.append(tp_child)
                                tp_visited.add(tp_child.val)
                    que.append(child)
                    visited.add(child.val)
        return True
```
结果：上述代码在 LeetCode 上测试超时；

### Solution-4

使用 DFS 遍历来检测有向图是否存在环：
```python {class=line-numbers}
class Solution:
    def no_loop_dfs(self, root_val, path_set):
        if root_val not in self.node_vals:return True
        if root_val in path_set:return False
        path_set.add(root_val)
        for child in self.record[root_val]:
            if not self.no_loop_dfs(child, path_set):
                return False
        path_set.remove(root_val)
        self.node_vals.remove(root_val)
        return True
    def canFinish(self, numCourses, prerequisites):
        """
        :type numCourses: int
        :type prerequisites: List[List[int]]
        :rtype: bool
        """
        from collections import defaultdict
        self.record = defaultdict(list)
        self.node_vals = set()
        for cur, pre in prerequisites:
            self.record[pre].append(cur)
            self.node_vals.update({cur, pre})
        while self.node_vals:
            root_val = self.node_vals.pop()
            self.node_vals.add(root_val)
            if not self.no_loop_dfs(root_val, set()):
                return False
        return True
```
上述代码在 LeetCode 上运行时间为 56 ms，优于 81% 的提交。
