---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 机器学习-图像识别

本篇博客主要演示如何训练机器识别出圆形、正方形、三角形等的图形。

## 图像与数组的转换

可以使用 Python 的 PIL(Python Imaging Library) 进行 图片\<--\>数组 的转换。

### 图片->数组
```python {cmd matplotlib=true}
import matplotlib.pyplot as plt
```


### 数组->图片

