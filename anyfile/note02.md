---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 概念总结

- 标准差的有偏估计：
$$\hat \sigma = \frac1{n-1}\sum_{i=1}^n (X_i-\bar X)$$

这是因为我们用样本的平均值 $\bar X$ 替代真实数据期望 $\mu$ 产生了标准差估计的偏差，所以要修正这一偏差，除以$n-1$，详情见：[为什么样本方差（sample variance）的分母是 n-1？ - 魏天闻的回答 - 知乎](https://www.zhihu.com/question/20099757/answer/26586088)

另外，`math.std`使用的是无偏估计，而 `pandas.DataFrame.describe` 使用的是有偏估计。
