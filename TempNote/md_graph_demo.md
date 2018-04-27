```python {cmd matplotlib=true}
from matplotlib_venn import venn2
from matplotlib_venn import venn3
import matplotlib.pyplot as plt
plt.figure(figsize=(4,3))
venn3((1,1,1,1,1,1,1,1))
plt.show()
```

```viz {}
digraph G{
    a->b
    subgraph cluster_ab{
        bgcolor=green
        label="a&b"
        a
        b
    }
}
```

```mermaid
graph LR
    def_aim("定义挖掘目标<br>○ 任务了解<br>○ 指标确定")==>data_collect("数据采集<br>○ 建模抽样<br>○ 质量把控<br>○ 实时采集")
    data_collect==>reorganize("数据整理<br>○ 数据探索<br>○ 数据清洗<br>○ 数据变换")
    reorganize==>construct_model("构建模型<br>○ 模式发现<br>○ 构建模型<br>○ 验证模型")
    construct_model==>model_evaluate("模型评价<br>○ 设定评价标准<br>○ 多模型对比<br>○ 模型优化")
    model_evaluate==>model_release("模型发布<br>○ 模型部署<br>○ 模型重构")
```

```flow
st=>start
A=>operation: Operation1
B=>condition: dec
C=>subroutine: sub
end=>end

st->A->B
B(yes,right)->C(right)->A
B(no)->end
```


```puml
@startuml
A -> B
B -> C
@enduml
```

```viz
graph G{
    node [shape=Mrecord]
    A [label="X"]
}
```

