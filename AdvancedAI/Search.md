---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 人工智能——搜索问题

> 在本篇文章中将要讲解所有的图搜索问题，并附上相关的代码，文章较长，请耐心阅读，最好配合代码进行试验。

搜索问题通常出现在图相关的算法中，我们需要对图进行搜索，实现需要知道图有几种，它们在计算机中如何表示。

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

下面我们用上面的类构建一个有向图：
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
通过 `circo` 引擎进行渲染后的图形为：

```viz {engine="circo"}
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



