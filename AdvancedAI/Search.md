---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 搜索问题-BFS/DFS/Dijkstra/Floyd

> 在本篇文章中将要讲解所有的图搜索问题，并附上相关的代码，文章较长，请耐心阅读，最好配合代码进行试验。

搜索问题通常出现在图相关的算法中，我们需要对图进行搜索，首先需要知道图有几种，它们在计算机中如何表示。

## 图

图根据节点之间的边是否有方向可以分为有向图和无向图。而边也可以设置一个数值，表示节点之间的“距离”，称为权值。

一个图应该具有如下的方法：
- `get_nodes()` : 获取所有节点的信息
- `insert_node(n, info)` : 插入节点`n`
- `get_node_info(n, info_name)` : 获取`n`节点的信息
- `insert_edge(n1, n2, info)` : 添加从`n1`到`n2`的边
- `get_edge_info(n1, n2, info_name)` : 获取边的信息
- `get_in_edges(n)` : 获取节点`n`的所有入边
- `get_out_edges(n)` : 获取节点`n`的所有出边
- `delete_edge(n1, n2)` : 删除从`n1`到`n2`的边
- `delete_node(n)` : 输出节点`n`


下面，我们用Python语言实现一个具有上述方法的图：
```python {cmd .line-numbers id="GraphDefine"}
class Graph:
    # directed 指定是否为有向图
    def __init__(self, directed=True):
        self.directed = directed
        # 存放入边
        self.in_edges = {}
        # 存放出边
        self.out_edges = {}
        #存放所有的边信息
        self.edges = {}

        self.nodes = {}
            
    # 注意: n_name 是字符串, 之间不能有 - 符
    def insert_node(self, n_name, info={}):
        self.nodes[n_name] = info.copy()

    def get_node_info(self, n_name, info_name):
        return self.nodes[n_name][info_name]

    def insert_edge(self, n1, n2, e_info={}):
        self.in_edges.setdefault(n2, []).append(n1)
        self.out_edges.setdefault(n1, []).append(n2)
        self.edges[n1+"-"+n2] = e_info.copy()
        if not self.directed:
            self.in_edges.setdefault(n1, []).append(n2)
            self.out_edges.setdefault(n2, []).append(n1)
            self.edges[n2+"-"+n1] = e_info.copy()
    def get_edge_info(self, n1, n2, info_name):
        return self.edges[n1+"-"+n2][info_name]
    def get_in_edges(self, n_name):
        return self.in_edges[n_name]
    def get_out_edges(self, n_name):
        return self.out_edges(n_name)
    def delete_edge(self, n1, n2):
        self.in_edges[n2].remove(n1)
        self.out_edges[n1].remove(n2)
        del self.edges[n1+"-"+n2]
        if not self.directed:
            self.in_edges[n1].remove(n2)
            self.out_edges[n2].remove(n1)
            del self.edges[n2+"-"+n1]
    def delete_node(self, n_name):
        del self.nodes[n_name]
        if n_name in self.in_edges:
            for n1_name in self.in_edges[n_name]:
                del self.edges[n1_name+"-"+n_name]
            del self.in_edges[n_name]
        if n_name in self.out_edges:
            for n2_name in self.out_edges[n_name]:
                del self.edges[n_name+"-"+n2_name]
            del self.out_edges
    # 自动按照 dot language 输出图的格式
    # 复制后通过dot language进行渲染即可实现可视化
    def to_dot(self, n_info_names=["label"], e_info_names=["label"]):
        str_prefix = '    '
        if self.directed:
            print("digraph G{")
            link_str = "->"
        else:
            print("strict graph G{")
            link_str = "--"
        for n in self.nodes:
            str_info = ""
            for iname in n_info_names:
                if iname in self.nodes[n]:
                    str_info += str(self.nodes[n][iname])+";"
            if len(str_info):
                str_info = "label=\""+str_info[:-1]+"\""
            print("{0}{1} [{2}]".format(str_prefix, n, str_info))
        for e in self.edges:
            str_info = ""
            for ename in e_info_names:
                if ename in self.edges[e]:
                    str_info += str(self.edges[e][ename])+";"
            if len(str_info):
                str_info = "label=\""+str_info[:-1]+"\""
            print("{0}{1}[{2}]".format(str_prefix, 
                    link_str.join(e.split("-")), str_info))
        print("}")
```

下面我们用上面的类构建一个边权相等的无向图：
```python {cmd continue="GraphDefine" id="graphNW"}
graph = Graph(False)
graph.insert_node('D',{"label":"Dunwich"})
graph.insert_node('B',{"label":"Blaxhall"})
graph.insert_node("H",{"label":"Harwich"})
graph.insert_node("F",{"label":"Feering"})
graph.insert_node("T",{"label":"Tiptree"})
graph.insert_node("C",{"label":"Clacton"})
graph.insert_node("M",{"label":"Maldon"})

graph.insert_edge('D','B', {'v':1})
graph.insert_edge('D','M', {'v':1})
graph.insert_edge('D','H', {'v':1})
graph.insert_edge('B','H', {'v':1})
graph.insert_edge('B','F', {'v':1})
graph.insert_edge('F','T', {'v':1})
graph.insert_edge('C','T', {'v':1})
graph.insert_edge('T','M', {'v':1})
graph.insert_edge('M','C', {'v':1})
graph.insert_edge('H','F', {'v':1})

graph.to_dot(n_info_names=['label'],
               e_info_names=['v'])
```
通过 `dot` 引擎进行渲染后的图形为：

```viz {engine="dot"}
strict graph G{
    D [label="Dunwich"]
    B [label="Blaxhall"]
    H [label="Harwich"]
    F [label="Feering"]
    T [label="Tiptree"]
    C [label="Clacton"]
    M [label="Maldon"]
    D--B[label="1"]
    B--D[label="1"]
    D--M[label="1"]
    M--D[label="1"]
    D--H[label="1"]
    H--D[label="1"]
    B--H[label="1"]
    H--B[label="1"]
    B--F[label="1"]
    F--B[label="1"]
    F--T[label="1"]
    T--F[label="1"]
    C--T[label="1"]
    T--C[label="1"]
    T--M[label="1"]
    M--T[label="1"]
    M--C[label="1"]
    C--M[label="1"]
    H--F[label="1"]
    F--H[label="1"]
}
```

## 搜索算法

一个问题给定初始位置和终止位置，求解从初始位置到终止位置的一条最佳路径称为搜索问题。之给定初始状态，要求解最佳的终态称为最优化问题。给定初始状态，要求解满足某些条件的终态称为约束满足问题(CSP)。

下面我们来看搜索中最常用的广度优先和深度优先。现在要寻找一条上图从`Dunwich`到`TipTree`的路径。

### 无权图的路径搜索

#### 广度优先搜索 BFS

```python {.line-numbers}
def breadth_first_search(g, n_start, n_end):
    path = {n_name:"" for n_name in g.nodes}
    nodes = []
    path[n_start] = n_start
    nodes.append(n_start)
    while len(nodes):
        cur_n = nodes.pop(0)
        for tmp_n in g.out_edges[cur_n]:
            if path[tmp_n] == "":
                path[tmp_n] = cur_n
                nodes.append(tmp_n)
                if tmp_n == n_end:
                    nodes.clear()
                    break
    node_path = [n_end]
    cur_n = n_end
    while path[cur_n]!="":
        if cur_n==path[cur_n]:break
        node_path.insert(0, path[cur_n])
        cur_n = path[cur_n]
    return node_path
print(breadth_first_search(graph, 'D', 'T'))
```
```
['D', 'M', 'T']
```

#### 深度优先搜索 DFS
```python {.line-numbers}
def depth_first_search(g, n_start, n_end):
    path = {n_name:"" for n_name in g.nodes}
    nodes = []
    path[n_start] = n_start
    nodes.append(n_start)
    while len(nodes):
        cur_n = nodes.pop(0)
        for tmp_n in g.out_edges[cur_n]:
            if path[tmp_n] == "":
                path[tmp_n] = cur_n
                nodes.insert(0, tmp_n)
                if tmp_n == n_end:
                    nodes.clear()
                    break
    node_path = [n_end]
    cur_n = n_end
    while path[cur_n]!="":
        if cur_n==path[cur_n]:break
        node_path.insert(0, path[cur_n])
        cur_n = path[cur_n]
    return node_path
print(depth_first_search(graph, 'D', 'T'))
```
```
['D', 'H', 'F', 'T']
```

通过比较可以发现，BFS和DFS的差别仅仅是在第11行，插入待检测的数组的位置。

#### BFS和DFS的比较

- 完备性：如果有解，则BFS、DFS都能返回一个解，而如果没有接，一定能评定出问题无解；
- 最优性：BFS选出的一定是最优解，而DFS选出的不一定是最优解；
- 空间复杂度：如果从起点`n_start`到`n_end`的路径最短路径长度为`m`，最长路径长度为`M`，节点的度为`d`。则BFS的空间复杂度为 $O(d^m)$，可见空间复杂度增长非常快，而DFS的空间复杂度最坏为 $O(M\cdot d)$，可以说比BFS小很多。
- 时间复杂度：与空间复杂度类似，BFS为$O(d^m)$，DFS为$O(M\cdot d)$。可能有的教材对时间复杂度的结论不一样，因为和图的存储结构、搜索方式有关，这里不做讨论。

上面两种都是在边的权重相同时（无权图），图的路径搜索。下面介绍在图的边权重不同时（有权图），图的路径搜索。

### 有权图的路径搜索

下面我们构建一个有向的图：
```python {cmd continue="GraphDefine"}
digraph = Graph()
digraph.insert_node('D',{"label":"Dunwich"})
digraph.insert_node('B',{"label":"Blaxhall"})
digraph.insert_node("H",{"label":"Harwich"})
digraph.insert_node("F",{"label":"Feering"})
digraph.insert_node("T",{"label":"Tiptree"})
digraph.insert_node("C",{"label":"Clacton"})
digraph.insert_node("M",{"label":"Maldon"})

digraph.insert_edge('D','B', {'v':15})
digraph.insert_edge('D','H', {'v':35})
digraph.insert_edge('B','H', {'v':22})
digraph.insert_edge('B','F', {'v':37})
digraph.insert_edge('F','T', {'v':6})
digraph.insert_edge('H','T', {'v':27})
digraph.insert_edge('C','T', {'v':66})
digraph.insert_edge('T','M', {'v':28})
digraph.insert_edge('M','C', {'v':1})
digraph.to_dot(n_info_names=['label'],
               e_info_names=['v'])
```
对应的图形为：
```viz {engine="circo}
digraph G{
    D [label="Dunwich"]
    B [label="Blaxhall"]
    H [label="Harwich"]
    F [label="Feering"]
    T [label="Tiptree"]
    C [label="Clacton"]
    M [label="Maldon"]
    D->B[label="15"]
    D->H[label="35"]
    B->H[label="22"]
    B->F[label="37"]
    F->T[label="6"]
    H->T[label="27"]
    C->T[label="66"]
    T->M[label="28"]
    M->C[label="1"]
}
```

我们希望找到从`Dunwich`到`Tiptree`的最短路径：

#### Dijkstra 算法

迪杰斯特拉算法用于寻找有权图中一个节点到另一个节点的最短路径。

迪杰斯特拉算法是从终点开始倒着寻找起点。

```python {.line-numbers}
def dijkstra(g, n_start, n_end):
    path = {n_name:{"next_node":"", "distance":float("inf"), 
                    "selected":False} for n_name in g.nodes}
    path[n_end]['distance'] = 0
    path[n_end]['selected'] = True
    cur_pos = n_end
    while cur_pos:
        if cur_pos in g.in_edges:
            for tp_node in g.in_edges[cur_pos]:
                if path[tp_node]["selected"]==False:
                    tp_distance = (path[cur_pos]['distance'] 
                        + g.edges[tp_node+"-"+cur_pos]['v'])
                    if tp_distance < path[tp_node]['distance']:
                        path[tp_node]['distance'] = tp_distance
                        path[tp_node]['next_node'] = cur_pos
        cur_pos = ""
        for tp_node in path:
            n_info = path[tp_node]
            if (not n_info['selected']) and n_info['distance']!=float('inf'):
                if cur_pos=="" or n_info['distance']<path[cur_pos]['distance']:
                    cur_pos = tp_node
        if cur_pos:
            path[cur_pos]['selected'] = True
    ret_path = [n_start]
    cur_pos = n_start
    while path[cur_pos]['next_node']:
        ret_path.append(path[cur_pos]['next_node'])
        cur_pos = path[cur_pos]['next_node']
    return ret_path, path[n_start]['distance']

print(dijkstra(digraph, 'D', 'T'))
```
返回的第一个值是路径，第二个是路径长度：
```
(['D', 'B', 'F', 'T'], 58)
```
无权图的路径搜索只是有权图搜索的一种特殊情况(图的权值都相等)，因此 Dijkstra 可以用于无权图，并且得到的也是一个最优路径：
```python
dijkstra(graph, 'D', 'T')
```
```
(['D', 'M', 'T'], 2)
```
#### Floyd 算法

```python {.line-numbers}
def floyd(g):
    path_matrix = {n1:{
            n2:{'next':'','d':float('inf')} 
            for n2 in g.nodes} 
        for n1 in g.nodes}
    for i in g.nodes:
        path_matrix[i][i]['d'] = 0
    for e in g.edges:
        n_from, n_to = e.split('-')
        path_matrix[n_from][n_to]['d'] = g.edges[e]['v']
        path_matrix[n_from][n_to]['next'] = n_to
    for k in g.nodes:
        for i in g.nodes:
            for j in g.nodes:
                path_matrix[i][k]['d']
                path_matrix[k][j]['d']
                jmp_d = path_matrix[i][k]['d']+path_matrix[k][j]['d']
                if (i!=j and path_matrix[i][j]['d'] > jmp_d):
                    path_matrix[i][j]['d'] = jmp_d
                    path_matrix[i][j]['next'] = k
    return path_matrix
# 从矩阵中取出指定的路径
def get_path(path_matrix, n_start, n_end, manual_call=True):
    if path_matrix[n_start][n_end]['d'] == float('inf'):
        print("No path from {0} to {1}".format(n_start, n_end))
    
    if path_matrix[n_start][n_end]['next']==n_end:
        return [n_start, n_end]
    jmp_node = path_matrix[n_start][n_end]['next']
    left_path = get_path(path_matrix, n_start, jmp_node)
    right_path = get_path(path_matrix, jmp_node, n_end)
    del right_path[0]
    return left_path + right_path

path_m = floyd(digraph)
print(get_path(path_m, 'D', 'T'), path_m['D']['T']['d'])
```
```
['D', 'B', 'F', 'T'] 58
```
使用Dijkstra的结果相同。

具体的算法思想可参考：<http://wiki.jikexueyuan.com/project/easy-learn-algorithm/floyd.html>

#### Dijkstra 和 Floyd 对比

- 功能：Dijkstra 寻找的给定一个点到指定的另一个点的最短路径；Floyd寻找图中各点之间的最短路径；

- 空间复杂度：$n$为节点的个数，则Dijkstra的空间复杂度为$O(n)$，Floyd的空间复杂度为$O(n^2)$

- 时间复杂度：Dijstra的时间复杂度为$O(n^2)$，Floyd的时间复杂度为$O(n^3)$









