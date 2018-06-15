---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 399. Evaluate Division

> <https://leetcode.com/problems/evaluate-division/description/>

## Description

Equations are given in the format `A / B = k`, where `A` and `B` are variables represented as strings, and `k` is a real number (floating point number). Given some queries, return the answers. If the answer does not exist, return `-1.0`.

**Example**:
Given `a / b = 2.0, b / c = 3.0`. 
queries are: `a / c = ?, b / a = ?, a / e = ?, a / a = ?, x / x = ?` . 
return `[6.0, 0.5, -1.0, 1.0, -1.0 ]`.

According to the example above:

> equations = `[ ["a", "b"], ["b", "c"] ]`
values = `[2.0, 3.0]`
queries = `[["a", "c"], ["b", "a"], ["a", "e"], ["a", "a"], ["x", "x"]]` 

The input is always valid. You may assume that evaluating the queries will result in no division by zero and there is no contradiction.

## Solution

### Solution-1

直接按顺序求解各个变量值，代入 `queries`：

```python {class=line-numbers}
class Solution(object):
    def calcEquation(self, equations, values, queries):
        """
        :type equations: List[List[str]]
        :type values: List[float]
        :type queries: List[List[str]]
        :rtype: List[float]
        """
        var_d = dict()
        for (s_v1, s_v2), r in zip(equations, values):
            if s_v1 in var_d:
                var_d[s_v2] = var_d[s_v1]/r
            elif s_v2 in var_d:
                var_d[s_v1] = var_d[s_v2]*r
            else:
                var_d[s_v2] = 1.0
                var_d[s_v1] = r
        rets = []
        for s_x1, s_x2 in queries:
            if not(s_x1 in var_d and s_x2 in var_d):
                rets.append(-1.0)
            else:
                rets.append(var_d[s_x1]/var_d[s_x2])
        return rets
```

上述代码在下面的实例中发生错误：

```python
s = Solution()
s.calcEquation(
[["a","b"],["e","f"],["b","e"]],
[3.4,1.4,2.3],
[["b","a"],["a","f"],["f","f"],["e","e"],["c","c"],["a","c"],["f","e"]])
```
输出为：
`[0.29411764705882354, 3.4, 1.0, 1.0, -1.0, -1.0, 2.3]`
实际结果应该为：
`[0.29411764705882354,10.947999999999999, 1.0, 1.0, -1.0, -1.0, 0.7142857142857143]`

原因：两个式子间产生冲突，例如：`a/b=1.0, c/d=2.0, c/b=1.0`,则按照上面的代码，由第一个等式计算的结果为：`a=1, b=1`；由第二个等式计算的结果为：`c=2, d=1`；这样，第三个等式产生冲突，得到`c=2,b=2`，第一个等式的 `b=1`被覆盖，因此产生错误；

### Solution-2
图的遍历：将每个变量看成一个节点，等式相当于节点之间的边，根据连通关系递推求出各个变量的值：
```python {class=line-numbers}
class Solution(object):
    def calcEquation(self, equations, values, queries):
        """
        :type equations: List[List[str]]
        :type values: List[float]
        :type queries: List[List[str]]
        :rtype: List[float]
        """
        from collections import defaultdict
        var_d = dict()
        edges_d = defaultdict(set)
        for i in range(len(equations)):
            s_v1, s_v2 = equations[i]
            edges_d[s_v1].add(i)
            edges_d[s_v2].add(i)
            
        # 计算各个变量的值
        next_points = []
        visited_indices = set()
        while len(edges_d):
            if len(next_points):
                cur_v = next_points.pop()
            else:
                cur_v = list(edges_d.keys())[0]
            if cur_v in edges_d:
                ed_indices = edges_d[cur_v]
                del edges_d[cur_v]
                for i in ed_indices-visited_indices:
                    s_v1, s_v2 = equations[i]
                    r = values[i]
                    if s_v1 in equations:next_points.append(s_v1)
                    if s_v2 in equations:next_points.append(s_v2)
                    if s_v1 in var_d:
                        var_d[s_v2] = var_d[s_v1]/r
                    elif s_v2 in var_d:
                        var_d[s_v1] = var_d[s_v2]*r
                    else:
                        var_d[s_v2] = 1.0
                        var_d[s_v1] = r
        rets = []
        for s_x1, s_x2 in queries:
            if not(s_x1 in var_d and s_x2 in var_d):
                rets.append(-1.0)
            else:
                rets.append(var_d[s_x1]/var_d[s_x2])
        return rets
```
上述代码在下面的实例中不是想要的结果：
```python
s = Solution()
s.calcEquation([["a","b"],["c","d"]],
[1.0,1.0],
[["a","c"],["b","d"],["b","a"],["d","c"]])
```
输出为：`[1.0, 1.0, 1.0, 1.0]`
实际应该为：`[-1.0, -1.0, 1.0, 1.0]`

原因：根据 `a/b=1.0, c/d=1.0`，其实不能推测出 `a/c` 的值，因为节点 `a` 和 `c` 并不在同一连通图中，我们只需要通过一个标记记录变量是否在同一个图中（即是否连通）即可。

### Solution-3

```python {class=line-numbers}
class Solution(object):
    def calcEquation(self, equations, values, queries):
        """
        :type equations: List[List[str]]
        :type values: List[float]
        :type queries: List[List[str]]
        :rtype: List[float]
        """
        from collections import defaultdict
        var_d = dict()
        edges_d = defaultdict(set)
        for i in range(len(equations)):
            s_v1, s_v2 = equations[i]
            edges_d[s_v1].add(i)
            edges_d[s_v2].add(i)
            
        next_points = []
        visited_indices = set()
        flag = 0
        while len(edges_d):
            if len(next_points):
                cur_v = next_points.pop()
            else:
                cur_v = list(edges_d.keys())[0]
                flag += 1
            if cur_v in edges_d:
                ed_indices = edges_d[cur_v]
                del edges_d[cur_v]
                for i in ed_indices-visited_indices:
                    visited_indices.add(i)
                    s_v1, s_v2 = equations[i]
                    r = values[i]
                    if s_v1 in edges_d:next_points.append(s_v1)
                    if s_v2 in edges_d:next_points.append(s_v2)
                    if s_v1 in var_d:
                        var_d[s_v2] = (var_d[s_v1][0]/r, flag)
                    elif s_v2 in var_d:
                        var_d[s_v1] = (var_d[s_v2][0]*r, flag)
                    else:
                        var_d[s_v2] = (1.0, flag)
                        var_d[s_v1] = (r, flag)
        rets = []
        for s_x1, s_x2 in queries:
            if not(s_x1 in var_d and s_x2 in var_d):
                rets.append(-1.0)
            else:
                if var_d[s_x1][1] != var_d[s_x2][1]:
                    rets.append(-1.0)
                else:
                    rets.append(var_d[s_x1][0]/var_d[s_x2][0])
        return rets
```
该代码通过测试，用时 33 ms，由于 75% 的提交。

### Solution-4

使用 DFS 求解一个节点到另一个节点的路径：
```python {class=line-numbers}
class Solution(object):
    def dfs(self, graph, v1, v2):
        if v1==v2:return 1.0
        if v1 in graph and v2 in graph[v1]:
            return graph[v1][v2]
        else:
            for next_v in graph[v1]:
                if next_v in self.visited:
                    continue
                # 避免重复搜索陷入死循环
                self.visited.add(next_v)
                tmp = self.dfs(graph, next_v, v2)
                if tmp!=-1: return graph[v1][next_v]*tmp
                self.visited.remove(next_v)
            else:
                return -1.0
    def calcEquation(self, equations, values, queries):
        """
        :type equations: List[List[str]]
        :type values: List[float]
        :type queries: List[List[str]]
        :rtype: List[float]
        """
        from collections import defaultdict
        graph = defaultdict(defaultdict)
        for (s_v1, s_v2),r in zip(equations, values):
            graph[s_v1][s_v2] = r
            if r:graph[s_v2][s_v1] = 1.0 / r
        ret = []
        for s_v1,s_v2 in queries:
            if not (s_v1 in graph and s_v2 in graph):
                ret.append(-1.0)
            else:
                self.visited = set([s_v1])
                ret.append(self.dfs(graph, s_v1, s_v2))
        return ret
```

Solution-3 和 Solution-4 的比较：

- Solution-3 计算出每个变量的值以及所属的连通图(用`flag`进行记录)，所以在计算`queries`中的等式时速度非常快。但是，如果添加一个新的等式规则进入 `equations`，并且新等式连接了两个不连通的图，那么整个图可能需要重构；这种方式的内存占用非常小；
<br>
- Solutions-4 在给出等式之后，使用深度遍历进行计算，所以计算`queries`中的等式时速度比 Solution-3 要慢很多，但是，如果添加一个新的等式规则进入 `equations` 只需要添加两条边进入 `graph` 即可；

总的来说，Solution-3 应该是比 Solution-4 要好一些。

