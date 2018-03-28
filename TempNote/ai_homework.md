# 高级人工智能作业

**姓名：丰华彬 &nbsp;&nbsp;&nbsp;&nbsp;学号：SA17011135**


<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

* [高级人工智能作业](#高级人工智能作业)
	* [题6.8](#题68)
		* [图的定义](#图的定义)
		* [按题目要求构建图](#按题目要求构建图)
		* [图的显示](#图的显示)
		* [着色算法](#着色算法)
		* [染色结果](#染色结果)
	* [题6.9](#题69)
		* [解答](#解答)

<!-- /code_chunk_output -->


## 题6.8
Consider the graph with 8 nodes A1, A2, A3, A4, H, T, F1, F2. Ai is connected to Ai+1 for all i, each Ai is connected to H, H is connected to T, and T is connected to each Fi. Find a 3-coloring of this graph by hand using the following strategy: backtracking with conflict-directed backjumping, the variable order A1, H, A4, F1, A2, F2, A3, T, and the value order R, G, B.

### 图的定义

为了实现题目的要求，我们需要先将图通过计算机表示出来，我们创建了一个`Graph`类，其有如下的方法：

- `get_nodes()` : 获取所有节点的信息
- `insert_node(n, info)` : 插入节点`n`
- `get_node_info(n, info_name)` : 获取`n`节点的信息
- `insert_edge(n1, n2, info)` : 添加从`n1`到`n2`的边
- `get_edge_info(n1, n2, info_name)` : 获取边的信息
- `get_in_edges(n)` : 获取节点`n`的所有入边
- `get_out_edges(n)` : 获取节点`n`的所有出边
- `delete_edge(n1, n2)` : 删除从`n1`到`n2`的边
- `delete_node(n)` : 输出节点`n`


如下代码所示，用Python语言定义一个图的类：
```python {cmd id="GraphDefine" .line-numbers}
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

### 按题目要求构建图
```python {cmd continue="GraphDefine" .line-numbers}
graph = Graph(False)
for i in ['A1','A2','A3','A4','H','T','F1','F2']:
    graph.insert_node(i)
for i in [1,2,3]:
    graph.insert_edge('A'+str(i),'A'+str(i+1))
for i in [1,2,3,4]:
    graph.insert_edge('A'+str(i),'H')
graph.insert_edge('H','T')
graph.insert_edge('T','F1')
graph.insert_edge('T','F2')
graph.to_dot()
```
### 图的显示
将上面的输出使用`neato`引擎进行渲染：

```viz {engine='neato'}
strict graph G{
    A1 []
    A2 []
    A3 []
    A4 []
    H []
    T []
    F1 []
    F2 []
    A1--A2[]
    A2--A1[]
    A2--A3[]
    A3--A2[]
    A3--A4[]
    A4--A3[]
    A1--H[]
    H--A1[]
    A2--H[]
    H--A2[]
    A3--H[]
    H--A3[]
    A4--H[]
    H--A4[]
    H--T[]
    T--H[]
    T--F1[]
    F1--T[]
    T--F2[]
    F2--T[]
}
```

### 着色算法
```python {.line-numbers}
nodes_seq = ['A1','H','A4','F1','A2','F2','A3','T']
color_seq = ["N",'R','G','B']
for n_name in graph.nodes:
    graph.nodes[n_name]['color'] = 'N'

cur_node_index = 0
while cur_node_index>=0 and cur_node_index<len(nodes_seq):
    cur_node_name = nodes_seq[cur_node_index]
    cur_node = graph.nodes[cur_node_name]
    cur_node_color_index = color_seq.index(cur_node['color'])
    if cur_node_color_index==len(color_seq)-1:
        cur_node['color'] = 'N'
        cur_node_index -= 1
        continue
    cur_node['color'] = color_seq[cur_node_color_index+1]
    # 判断是否和周围的节点产生冲突
    for neighbor_name in graph.in_edges[cur_node_name]:
        if graph.nodes[neighbor_name]['color']==cur_node['color']:
            break
    else:
        # 没有产生冲突
        cur_node_index += 1
        continue
        
if cur_node_index<0:
    print("Can't fill color with these rules")
else:
    graph.to_dot(['color'])
```
得到的结果为：
```
A1 [label="R"]
A2 [label="B"]
A3 [label="R"]
A4 [label="B"]
H [label="G"]
T [label="B"]
F1 [label="R"]
F2 [label="R"]
```

### 染色结果

```viz {engine='neato'}
strict graph G{
    A1 [style=filled,fillcolor="red"]
    A2 [style=filled,fillcolor="#00bfff"]
    A3 [style=filled,fillcolor="red"]
    A4 [style=filled,fillcolor="#00bfff"]
    H [style=filled,fillcolor="green"]
    T [style=filled,fillcolor="#00bfff"]
    F1 [style=filled,fillcolor="red"]
    F2 [style=filled,fillcolor="red"]
    A1--A2[]
    A2--A3[]
    A3--A4[]
    A1--H[]
    A2--H[]
    A3--H[]
    A4--H[]
    H--T[]
    T--F1[]
    T--F2[]
}
```

## 题6.9 
Explain why it is a good heuristic to choose the variable that is most constrained but the value that is least constraining in a CSP search.

### 解答
在选择变量时，选择限制条件最多的变量就是为了使冲突尽可能早的发生，这样可以避免很多无用的计算。

而选择值时，选择限制条件最少的值可以使得该值和后面变量的值尽可能少地产生冲突，以使得最大可能地得到解。毕竟我们的目标是为了得到一个解而不是产生冲突。
