---
export_on_save:
 html: true
---

@import "/blog_head.md"

# AI 之线性代数公式


**循环改变迹运算中相乘矩阵的顺序不影响结果：**
$$Tr\big(\prod_{i=1}^n F^{(i)}\big)=Tr\big(F^{n}\prod_{i=1}^{n-1}F^{(i)}\big)$$
例如，设矩阵 $A\in\Bbb R^{m\times n}$，矩阵 $B\in\Bbb R^{n\times m}$，则有：$Tr(AB)=Tr(BA)$

**对称矩阵的性质**
- 对称矩阵一定是方正
- 对称矩阵一定可以相似对角化
设 A 为对称矩阵，则 $P^TAP=\Lambda$，其中 $\Lambda$ 由 $A$ 的特征值组成，$P$ 由特征值对应的特征向量组成，并且位置一一对应。  



