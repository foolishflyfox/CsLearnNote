---
export_on_save:
 html: true
---
@import "/blog_head.md"

# Dot 语言总结

<small>本篇博客可能是图最多的一篇吧。</small>

> **参考：**
> - [DOT语言学习笔记](https://github.com/uolcano/blog/issues/13)
> - [DOT语言-维基百科](https://zh.wikipedia.org/wiki/DOT%E8%AF%AD%E8%A8%80)
> - [dotguide.pdf](http://www.graphviz.org/pdf/dotguide.pdf)
> - [dot绘图语言](https://frapples.github.io/2016/11/16/dot-language/
)

可以通过 [vscode/atom + markdown preview enhance](https://shd101wyy.github.io/markdown-preview-enhanced/#/zh-cn/diagrams?id=graphviz)构建能够识别Dot语言的编辑器，具体方法请自行Google。本内容摘要讲解 Dot 语言的语法。

## 5种引擎

Markdown Preview Enhanced 使用 Viz.js 来渲染 dot语言 图形。其支持的引擎有：`dot`(默认)、`circo`、`neato`、`twopi` 和 `osage`；

5中引擎有各自的特点：
- **dot** : 节点自上而下进行绘制；
- **circo** : 圆环布局
- **neato** : 基于 spring-model算法，基于斥力+张力的布局
- **twopi** : 径向布局
- **osage** : 紧凑型布局

如代码：
```
digraph G{
    A->B
    B->C
    B->D
    B->E
    D->F
    A->F
}
```

下面是5种引擎下的显示：

### dot 引擎
dot 引擎主要用于绘制有向图，也可以绘制无向图。
```viz {engine="dot"}
digraph G{
    graph [label="dot engine"]    
    A->B
    B->C
    B->D
    B->E
    D->F
    A->F
}
```
### circo 引擎

```viz {engine="circo"}
digraph G{
    graph [label="circo engine"]    
    A->B
    B->C
    B->D
    B->E
    D->F
    A->F
}
```

###  neato 引擎
```viz {engine="neato"}
digraph G{
    graph [label="neato engine"]    
    A->B
    B->C
    B->D
    B->E
    D->F
    A->F
}
```

### twopi 引擎
```viz {engine="twopi"}
digraph G{
    graph [label="twopi engine"]    
    A->B
    B->C
    B->D
    B->E
    D->F
    A->F
}
```

### osage 引擎
```viz {engine="osage"}
digraph G{
    graph [label="osage engine"]    
    A->B
    B->C
    B->D
    B->E
    D->F
    A->F
}
```

下面的语法使用默认的 `dot` 引擎；

## dot语言语法

### dot 的注释

dot 支持C和C++风格的注释，有 // 和 /* */ 两种。

### 绘制有向图

```viz {hide=false}
digraph G{
    A->B
}
```
### 绘制无向图
```viz {hide=false}
graph G{
    A--B
}
```
### 一个节点到多个节点
```viz {hide=false}
graph G{
    A--{B, C}
}
```
### 多个节点到多个节点
```viz {hide=false}
digraph G{
    {A,B}->{A,C,D,E}
}
```

### 链
```viz {hide=false}
digraph G{
    {A,B}->C->{D,E}
}
```
## 对图中的元素进行设置

### 节点属性设置

#### 设置节点标签
```viz {hide=false}
digraph G{
    A [label="寝室"]
    B [label="教室"]
    A->B
}
```

#### 设置节点形状
```viz {hide=false}
digraph G{
    A [label="寝室", shape=circle]
    B [label="教室", shape=box]
    A->B
}
```
形状有很多种，总结如下：
```viz {hide=false}
digraph G{
    A [label="ellipse" shape=ellipse]
    B [label="cycle", shape=circle]
    C [label="box", shape=box]
    D [label="polygon", sides=7, shape=polygon]
    E [label="diamond", shape=diamond]
    F [label="none", shape=none]
    G [label="doublecircle", shape=doublecircle]
    H [label="folder",shape=folder]
    I [label="egg",shape=egg]
    J [label="point",shape=point]
    K [label="star",shape=star]
    L [label="square",shape=square]
    M [label="cylinder",shape=cylinder]
    N [label="box3d",shape=box3d]
    O [label="rarrow",shape=rarrow]
    P [label="rpromoter",shape=rpromoter]
    A->B->I->M
    C->D->J->N
    E->F->K->O
    G->H->L->P
}
```
实在太多了，上面只是列出了常用的一些常用的，你想要的所有形状，包括颜色、大小、背景色等等，都可以设置，详情请移步：<https://www.graphviz.org/doc/info/shapes.html> ；

#### 设置节点的颜色

```viz {hide=false}
digraph G{
    A[shape=box,
    color=red, //红色边框
    style=filled,fillcolor="#00bfff", //蓝色填充
    fontcolor=green, //绿色字体
    fontsize=25 //设置字体大小
    ]
}
```

#### 在节点中插入表格

```viz {hide=false}
graph G{
    A [shape=box, label=<
    <table>
        <th><td colspan="3">1</td></th>
        <tr><td>1</td><td>2</td><td>3</td></tr>
    </table>
    >]
}
```

### 边的属性

#### 边的样式
```viz {hide=false}
digraph G{
    rankdir=LR
    a,b,c,d,e [shape=point]
    a->b [label=" solid",style=solid]
    b->c [label=" bold",style=bold]
    c->d [label=" dashed",style=dashed]
    d->e [label=" dotted",style="dotted"]
}
```

#### 箭头样式

##### 设置箭头方向

```viz {hide=false}
digraph G{
    rankdir=LR
    a,b [shape=point]
    a->b [dir=both, label="both"]
}
```

##### 设置箭头大小

```viz {hide=false}
digraph G{
    rankdir=LR
    A->B [arrowsize=2]
}
```

#### 设置箭头颜色

```viz {hide=false}
digraph G{
    a,b [shape=point]
    a->b [color=red]
}
```

#### 设置箭头形状
```viz {hide=false}
diagraph G{
    rankdir=LR
    a,b,c,d,e [shape=point]
    a->b [arrowhead=box]
    b->c [arrowhead=diamond]
    c->d [arrowhead=curve]
    d->e [arrowhead=dot]
}
```

## 全局属性设置

### graph

- `label` ：定义图的标签
- `bgcolor` ：定义图的背景
- `fontname` ：定义图的字体名称
- `fontsize` ：定义图的字体大小
- `rankdir` ：定义图的方向，可选：TB、BT、LR、RL

```viz {hide=false}
digraph G{
    graph [label="graph set",
    bgcolor="#00bfff", 
    fontsize=10, rankdir="LR"]
    A->B->C
}
```

### node

```viz {hide=false}
digraph G{
    node [shape=box, style=filled, fillcolor="pink"]
    A->{B,C}
}
```

### edge
```viz {hide=false}
digraph G{
    edge [style="dashed", color="#00bfff",dir=both]
    A->{B,C}
}
```

## 应用

实际应用请看 [各种常用图的绘制](https://foolishflyfox.github.io/CsLearnNote/Editor/CommonGraph.html)




