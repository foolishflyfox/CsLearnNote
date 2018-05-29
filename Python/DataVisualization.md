---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 数据可视化

## 数组的显示

### cmap 的使用

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
![](/Python/assets/cmap.jpg)

## 用 matplotlib 绘图

首先应该说明一下绘图库 **seaborn** 和 **matplotlib** 的关系：
Seaborn其实是在matplotlib的基础上进行了更高级的API封装，从而使得作图更加容易，在大多数情况下使用seaborn就能做出很具有吸引力的图，而使用matplotlib就能制作具有更多特色的图。应该把Seaborn视为matplotlib的补充，而**不是替代物**。

所以，不是说为了图方便直接掌握 **seaborn** 绘图就好了，很多时候，**matplotlib** 绘图更加方便灵活。

绘图必须引入的库：
```python
import matplotlib
import matplotlib.pyplot as plt
```

### 绘制直方图

绘制提供了数据的直方图，可以使用函数 `plt.bar`

- `plt.figure(figsize=(10, 7))`：指定绘制图的大小；
<br>
- `plt.bar(left, height, width=0.8, bottom=None, hold=None, data=None, **kwargs)`
    - `left`：直方图指定位置条形的中心点对应的 x 位置
    - `height`：对应位置条形的 y 值
    - `weight`：对应位置条形的宽度
    - `color`：颜色，如 “#00bffff” 指定颜色为天蓝色，也可以写为 "deepskyblue"
    - `alpha`：透明度，0~1 之间
    - `label`：对应的条形名称，需要调用`plt.legend`才能显示
    - `edgecolor`：条形边框颜色
    - `lw`：也可以记为`linewidth`，条形边框的宽度

```python
import matplotlib
import matplotlib.pyplot as plt

colors = ["deepskyblue", "#A60628"]
prior = [1/21.0, 20/21.0]
plt.figure(figsize=(10,7))
plt.bar([0., 0.7], prior, width=0.25, alpha=0.7, 
        color=colors[0], label="prior distribution",
        lw="2",edgecolor="#000000")
posterior = [0.087, 1-0.087]
plt.bar([0.+0.25, 0.7+0.25], posterior, width=0.25, alpha=.7,
       color=colors[1], label="posterior distribution",
       lw="2", edgecolor="black")
plt.legend(loc="upper left")
plt.ylim(0,1.2)
plt.xticks([0.125, 0.7+0.25/2], ['Librarian', "Farmer"], fontsize=15)
plt.title("Prior and posterior probilities of Steve's occupation")
plt.ylabel("Probability", fontsize=15)
plt.show()
```

显示为：
![](/Python/assets/20180529233508.png)
