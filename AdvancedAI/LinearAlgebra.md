---
export_on_save:
 html: true
---

@import "/blog_head.md"

# AI 之线性代数公式


**循环改变迹运算中相乘矩阵的顺序不影响结果：**
$$Tr\big(\prod_{i=1}^n F^{(i)}\big)=Tr\big(F^{n}\prod_{i=1}^{n-1}F^{(i)}\big)$$
例如，设矩阵 $A\in\Bbb R^{m\times n}$，矩阵 $B\in\Bbb R^{n\times m}$，则有：$Tr(AB)=Tr(BA)$




