---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 各种常用图的绘制

在实际工作中，写文档时，我们经常要绘制各种图，下面对各种常见图的绘制都给出一个实例。

首先，需要装好 vscode/atom + markdown preview enhance，并在本地装好各种渲染引擎。如要渲染 vscode/atom 中的`viz`代码块，需要使用 `graphviz` 软件，安装方法非常简单，在 Linux 执行 `sudo apt install graphviz`，在 mac os 下执行 `brew install graphviz`。

如果想要将用dot语法编写的文件转换成 png/jpg/svg/pdf 等，使用方法为：
```
<cmd> <input-file> -T <format> -o output
```
如下例：
```
circo xxx.gv -T png -o xxx.png
```
用 circo 引擎绘制，输出png格式的文件。`<cmd>` 还可以用 *dot*、*osage*、*neato*、*twopi*。

另外，使用 `brew` 时国外的源通常较慢，可以使用国内的源，如中科大或者清华的镜像，方法：[替换及重置Homebrew默认源](https://lug.ustc.edu.cn/wiki/mirrors/help/brew.git)。

如果 Linux 的安装较慢，也可以修改`/etc/apt/sources.list`，具体方法参见 : <http://wiki.ubuntu.org.cn/%E6%BA%90%E5%88%97%E8%A1%A8> 。

在 vscode/atom 绘图方法：<https://shd101wyy.github.io/markdown-preview-enhanced/#/zh-cn/diagrams>

## 流程图

使用 flow charts 进行绘制，下面的例子是使用递归方法求解阶乘的函数：
```c
int factorial(n){
    if(n<0){
        printf("错误:n小于0")
        return -1;
    }
    if(n==0)
        return 1;
    return factorial(n-1)*n;
}
```

其流程图为：
```flow {hide=false}
st=>start: 开始:fractial(n)执行
lt_0=>condition: n小于0?
output_lt0=>inputoutput: 打印错误
rt_neg1=>operation: 返回-1
eq_0=>condition: n等于0?
rt_1=>operation: 返回1
calc_sub=>subroutine: 返回n×fractial(n-1)

end=>end: 结束

st->lt_0
lt_0(yes)->output_lt0->rt_neg1->end
lt_0(no)->eq_0(yes)->rt_1->end
eq_0(no)->calc_sub->end
```
**注意：**
- 关键字`start`、`operation`、`conditions`、`subroutine`、`inputoutput`、`end`和冒号之间不能有空格
- 冒号和之后的标签名之间必须有空格

## 数据的结构

下面是网购中的一条消费记录的数据结构：

```viz {hide=false}
digraph G{
    node [shape=record]
    consume [label="<customer>消费者|<goods>商品|<prince>价格|购买日期"]
    customer [label="id号|VIP?|生日"]
    goods [label="<productor>生产商|
    {生成地|生产日期|<price>建议价格|保质期}"]
    productor [label="{厂商编号|法人代表}"]

    consume:customer->customer
    consume:goods->goods
    goods:productor->productor
    consume:price->goods:price
    // 子图一定要以cluster为前缀
    subgraph cluster_gr{
        bgcolor=green
        label="goods relative"
        goods
        productor
    }
}
```
## 函数的调用

```viz {hide=false}
digraph G{
    makestring [label="make a \nstring"]
    compare [style=filled, fillcolor=gray, 
    shape=box]

    main [shape=box]
    main->parse
    main->init [style=dotted]
    main->printf [style=bold, color=red,
    label="100 times"]
    main->cleanup
    parse->execute
    execute->{printf, makestring}
    init->makestring
    execute->compare [color=red]
}
```

## 二叉树

```viz {hide=false}
digraph G{
    node [shape=record, height=.1]
    l1 [label="<left>|<f1>G|<right>"]
    l2_1 [label="<left>|<f1>E|<right>"]
    l2_2 [label="<left>|<f1>R|<right>"]
    l3_1 [label="<left>|<f1>B|<right>"]
    l3_2 [label="<left>|<f1>F|<right>"]
    l3_3 [label="<left>|<f1>H|<right>"]
    l3_4 [label="<left>|<f1>Y|<right>"]
    l4_1 [label="<left>|<f1>A|<right>"]
    l4_2 [label="<left>|<f1>C|<right>"]    
    l1:left->l2_1:f1
    l1:right->l2_2:f1
    l2_1:left->l3_1:f1
    l2_1:right->l3_2:f1
    l2_2:left->l3_3:f1
    l2_2:right->l3_4:f1
    l3_1:left->l4_1:f1
    l3_1:right->l4_2:f1
}
```

## 哈希表

```viz {hide=false}
digraph G{
    graph [rankdir=LR, nodesep=.05]
    node [shape=record]
    hash [label="<1>|<2>|<3>|<4>|<5>|<6>|<7>|<8>", width=0.1]
    node [width=1.5,height=0.1]
    node1 [label="{<head>n14|719|}"]
    node2_1 [label="{<head>a1|805|<tail>}"]
    node2_2 [label="{<head>o15|794|}"]
    node3 [label="{<head>i9|718|}"]
    node6_1 [label="{<head>e5|989|<tail>}"]
    node6_2 [label="{<head>s19|659|}"]
    node7 [label="{<head>t20|959|}"]
    hash:1->node1:head
    hash:2->node2_1:head
    node2_1:tail->node2_2:head
    hash:3->node3:head
    hash:6->node6_1:head
    node6_1:tail->node6_2:head
    hash:7->node7:head
}
```

## 思维导图

```viz {hide=false}
digraph G{
    label="Dot language learning map"
    rankdir=LR
    node [shape=Mrecord, fontsize=12]
    edge [arrowhead=dot,color="#afd275"]
    dot_language [label="DOT\n语言", style=filled, fillcolor="#00bfff"]

    grammar_string [label="字符串"]

    id_char [label="字符"]
    id_string [label="字符串"]

    dot_language->{"布局器", "语法"}
    "布局器"->{dot, circo, neato, twopi, osage}

    语法->{ID, "关键字", grammar_string,
    "分组符号", "属性"}

    ID->id_char->"字母\n数字\n下划线"
    ID->id_string->{"双引号包裹", <尖括号包裹>}

    "关键字"->{"graph","digraph","subgraph",
    "edge","node","strict"}
    "subgraph"->"cluster"

    grammar_string->{"换行:\\n", "拼接:+", "转义","实体字符"}
    "转义"->{"\\*", "\\\\"}

    "分组符号"->{"中括号:属性","大括号:节点"}

    "属性"->{"label","shape","style","fillcolor","bgcolor",
    "color","fontcolor","fontname","fontsize","rankdir",
    "dir","arrowhead","arrowtail","arrowsize"}
}
```


## 备注

还有UML之类的图，由于接触比较少，以后用到再进行补充...

