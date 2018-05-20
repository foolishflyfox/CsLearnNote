---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 机器学习-图像识别

> 参考：[matlab 中的 colormap](http://blog.sciencenet.cn/blog-360646-465608.html)、[利用 matplotlib 中 imshow 函数绘图](https://blog.csdn.net/shuke1991/article/details/50462580)

本篇博客主要演示如何训练机器识别出圆形、正方形、三角形等的图形。

## 数组的显示

创建一个的数组：
```python
tp = np.arange(-3, 3, 0.01)
x, y = np.meshgrid(tp, tp)
digit_a = (x**2+y**2)**.5
```
对该数组以各种 `cmap` 进行可视化：
```python
import matplotlib
import matplotlib.pyplot as plt
def show_array(a, n, cmap=None):
    plt.subplot(1, 4, n)
    plt.title(cmap)
    if cmap : plt.imshow(a, cmap)
    else : plt.imshow(a)
    plt.axis('off')

plt.figure(figsize=(15, 15))
# 默认情况
show_array(digit_a, 1)
# gray 最小值-黑色 最大值-白色 
show_array(digit_a, 2, 'gray')
# binary 最小值-白色 最大值-黑色
show_array(digit_a, 3, 'binary')
# flag 包含红色、白色、绿色和黑色
show_array(digit_a, 4, 'flag')
plt.show()

plt.figure(figsize=(15,15))
# cool 包含青绿色和品红色的阴影色, 冷色调
show_array(digit_a, 1, 'cool')
# hot 从黑色平滑过度到红色、橙色和黄色的背景色，然后到白色，红外成像
show_array(digit_a, 2, 'hot')
# jet 从蓝色到红色，中间经过青绿色、黄色和橙色
show_array(digit_a, 3, 'jet')
# hsv 从红色，变化到黄色、绿色、青绿色、品红色，返回到红色
show_array(digit_a, 4, 'hsv')
plt.show()
plt.figure(figsize=(15,15))


# pink 柔和的桃红色
show_array(digit_a, 1, 'pink')
# prism 重复这六种颜色：红色、橙色、黄色、绿色、蓝色和紫色
show_array(digit_a, 2, 'prism')
# bone 具有较高的蓝色成分的灰度色图
show_array(digit_a, 3, 'bone')
# copper 从黑色平滑过渡到亮铜色
show_array(digit_a, 4, 'copper')
plt.show()

plt.figure(figsize=(15, 15))
# spring 包含品红色和黄色的阴影颜色
show_array(digit_a, 1, 'spring')
# summer 包含绿色和黄色的阴影颜色
show_array(digit_a, 2, 'summer')
# autumn 从红色平滑变化到橙色，然后到黄色
show_array(digit_a, 3, 'autumn')
# winter 包含蓝色和绿色的阴影色
show_array(digit_a, 4, 'winter')
plt.show()
```
效果为：
![](/AdvancedAI/assets/cmap.jpg)


