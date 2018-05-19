---
export_on_save:
 html: true
---

@import "/blog_head.md"

# Python图像处理库PIL

PIL（Python Image Library）用于对图像的处理。该库对众多的图片格式提供支持，图像在内存中高效地存储，并且提供了众多的图像处理函数。

该库主要提供了3方面的功能：

- 图片的存储
- 图片的显示
- 图片的处理

## 使用Image类

在 PIL 中，最核心的类当属 `Image` 了。你可以通过多种方式创建 `Image` 类的实例，比如加载一个图片、处理另一个Image实例、或者直接从0开始创建。

### 加载一个图片

```python
from PIL import Image
im = Image.open("logo_mid.png")
```
如果成功，该函数将会返回一个 `Image` 对象，你可以使用实例属性对所加载的图片进行测试：
```python
>>> print(im.format, im.size, im.mode)
PNG (256, 256) RGBA
```
- `im.format` : 源文件的格式，如 PNG/JPEG/PPM/BMP 等，如果你的 im 文件并不是从文件加载而来，该属性为 None
<br>
- `im.size` : 一个2元组，分别表示图片的宽度和高度
<br>
- `im.mode` : 说明了图片的通道数和通道名，同时也说明了每个像素的类型和深度，通常来说，**L**（Luminance） 表示灰度图，**RGB** 表示彩色图，**CMYK** 表示印刷四分色图

如果图片不能打开（可能是由于指定的路径不是一个图片文件），将会抛出一个 `IOError` 异常。

**注意：open 之后并没有将图片实际加载到内存中，只是读取了图片文件的头，只有真正设计到图片内容的操作才会去真正获取图片像素信息，所以如果只想获取图片的长和宽，直接用 open 就可以了，就算超大的图片也不会浪费内存。**

获取了 `Image` 实例后，就可以对其进行操作了，如可以通过 `im.show()` 对其进行显示。

### 图片的读写

- `Image.open` : 打开图片文件，返回的是`PIL.Image.Image` 类型的一个实例
- `im.save` : 保存图片，如果没有提供 `format` 参数，将按照文件名的后缀指定的格式保存文件

### 剪切-粘贴-合并

`Image` 类中包含了对图片的区域进行处理的方法。我们可以通过 `crop()` 方法从图片中抽出一个子矩形区域。

#### 复制子矩形

```python
box = (50, 50, 50+40, 50+30)
region = im.crop(box)
```
`region` 通过一个4元组(left, upper, right, lower)指定。PIL将图片左上角的点的坐标设为`(0, 0)`。上面的代码中，`region` 的宽度和高度为 `40, 30`。

#### 处理子区域并回贴

```python
region = region.transpose(Image.ROTATE_180)
im.paste(region, box)
```
在使用 `paste` 方法时要注意`region` 的尺寸和`box`所指定的相同。另外 `region` 不能扩展到 `im` 之外。另外，`region` 的模式不一定要和 `im` 的模式一致，若不一致，将会对其进行自动转换。

#### 图片滚动

```python
from PIL import Image
im = Image.open("logo_mid.png")
def roll(image, delta):
    xsize, ysize = image.size
    delta %= xsize
    if delta==0:
        return image
    part1 = image.crop((0, 0, delta, ysize))
    part2 = image.crop((delta, 0, xsize, ysize))
    part1.load() # PIL 4.2.1 貌似可以去掉
    part2.load()
    image.paste(part2, (0, 0, xsize-delta, ysize), part2)
    image.paste(part1, (xsize-delta, 0, xsize, ysize))
    return image
roll(im, 75)
```

`paste` 方法有一个 `mask` 参数。对于RGBA的图像，如果 paste 的第一个参数`im` 和第三个参数 `mask` 相同，表示只粘贴有颜色的部分，而不粘贴无颜色的部分。

上面代码的结果为：
<div align="center">
<img src="/Python/assets/20180519114957.png">
</div>

#### split/merge
PIL 对于多通道的图片，如RGB图片，可以分别对其中的某个通道进行操作。`split` 方法创建一系列的新图片，每个图片包含了一个通道。
```python
# 将 mode 为 rgba 的 png 分成多个通道
r,g,b,a = im.split()
```
其中 `a` 通道的分量为：
<div align="center">
<img src="/Python/assets/20180519160304.png">
</div>

alpha 通道通常用作不透明度，其值为0表示完全透明(transparent)，255表示完全不透明(opaque)。

对于单通道的图片，`split()` 直接返回该图片。

可以通过 `merge` 方法将通道合成：
```python
im = Image.merge('RGBA', (r,g,b,a))
```

可以通过将不同的图片 `merge`：
```python
dog_im = Image.open('dog.jpeg')
leaf_im = Image.open('leaf.png')
r, g, b, a = leaf_im.split()
dog_in_leaf = Image.merge("RGBA", (*dog_im.split(), a))
```
`dog_in_leaf` 的效果为：
<div align="center">
<img src="/Python/assets/20180519170306.png">
</div>

## 几何变换

`PIL.Image.Image` 类包含了 `resize` 和 `rotate` 操作对图片进行几何变换。其中 `resize` 需要给出一个tuple类型的参数作为新的大小，`rotate` 需要给出一个角度，图片会安装该角度进行逆时针旋转。

### resize
```python
mini_im = im.resize((50, 50))
```
图片变为：
<div align="center">
<img src="/Python/assets/20180519162206.png">
</div>

### rotate
```python
rotate45_im = im.rotate(45)
```
图片变为：
<div align="center">
<img src="/Python/assets/20180519162536.png">
</div>

图片旋转90度，既可以用 `im.rotate(90)` 方法，也可以用`im.transpose(Image.ROTATE_90)` 方法。

### 图片变位

```python
out = im.transpose(Image.FLIP_LEFT_RIGHT) # 左右翻转
out = im.transpose(Image.FLIP_TOP_BOTTOM) # 上下翻转
out = im.transpose(Image.ROTATE_90)       # 旋转 90°
out = im.transpose(Image.ROTATE_180)      # 旋转 180°
out = im.transpose(Image.ROTATE_270)      # 旋转 270°
```

## 颜色变换

### mode 转变

```python
im_L = im.convert('L')
```
将 `im` 的 `mode` 转变为 `L`，原来可能是 `RGB`或`RGBA`。

注意，不能将 mode 为 `RGBA` 的图片保存为 `jpeg`，可以通过两种方法进行保存：
```python
# method 1 : convert mode to RGB, then save
im.convert('RGB').save('im.jpeg')
# method 1 : split RGBA -> merge RGB -> save
r, g, b, a = im.split()
Image.merge('RGB', (r,g,b)).save('im.jpeg')
```

### 图片特效操作

Image实例的`filter`方法可以实现修图效果：
```python
from PIL import ImageFilter
im = Image.open('logo_mid.png')
im = im.resize((128, 128))
im.filter(ImageFilter.BLUR).save("f_blur.png") # 模糊化
im.filter(ImageFilter.CONTOUR).save("f_contour.png") # 获取轮廓
im.filter(ImageFilter.DETAIL).save("f_detail.png") # 获取细节
im.filter(ImageFilter.EDGE_ENHANCE).save("f_edge_enhance.png") # 增强细节
im.filter(ImageFilter.EDGE_ENHANCE_MORE).save("f_edge_enhance_more.png")
im.filter(ImageFilter.EMBOSS).save('f_emboss.png') # 浮雕化
im.filter(ImageFilter.FIND_EDGES).save('f_edges.png') # 获取边缘
im.filter(ImageFilter.SHARPEN).save('f_sharpen.png') # 锐化
im.filter(ImageFilter.SMOOTH).save('f_smooth.png') # 平滑
im.filter(ImageFilter.SMOOTH_MORE).save('f_smooth_more.png')
```
<div align="center">
<img src="/Python/assets/filter.png">
</div>

### 像素操作

可以通过`point`对每个像素进行操作，如：
```python
im.point(lambda i:i*1.5)
```
将图片颜色调亮，效果：
<div align="center">
<img src="/Python/assets/20180519222008.png" />
</div>

可以通过 `crop`、`point`、`paste`的结合，对指定的区域进行操作。

### 对特定通道操作
将图片像素点中蓝色通道为值`>100`的所有像素，蓝色分量保留，红绿分量去除：
```python
source = im.split()
R, G, B, A = 0, 1, 2, 3
# select regions where blue is less than 100
mask = source[B].point(lambda i:i<100 and 255)
# process the red band and green band
out_r = source[R].point(lambda i:0)
out_g = source[G].point(lambda i:0)
# paste the processed band back, but only where blue was < 100
source[R].paste(out_r, None, mask)
source[G].paste(out_g, None, mask)
# bulid a new multiband image
Image.merge(im.mode, source)
```
结果为：
<div align="center">
<img src="/Python/assets/20180519224616.png" />
</div>

### 更多效果

你可以使用 [ImageEnhance](https://pillow.readthedocs.io/en/5.1.x/reference/ImageEnhance.html#module-PIL.ImageEnhance) 模块进行更高级的图片处理。

例如，你可以改变对比度、亮度、颜色平衡、锐化等。

```python
from PIL import ImageEnhance
enh = ImageEnhance.Brightness(im)
# 40% less brightness
enh.enhance(0.6)
```
<div align="center">
<img src="/Python/assets/20180519225342.png" />
</div>


## 动画图像

PIL 提供了基本的方法使用户能够处理动图。支持的动图包括：FLI/FLC，GIF 和一些专业的格式。TIFF 文件可以包含多于一个的帧。

### 读取动图

```python
from PIL import Image

im = Image.open('animation.gif')
im.seek(1) # 跳到第一秒的位置

try:
    while 1:
        im.seek(im.tell()+1)
        # do something to im
except EOFError:
    pass # end of sequence
```
在读取动图结束的时候，`seek` 函数将会弹出一个 `EOFError` 异常。

你也可以通过迭代器进行遍历：
```python
from PIL import ImageSequence
for frame in ImageSequence.Iterator(im):
    # do something to frame
    ...
```

### 添加标记

PIL 允许你对图片添加文本、图表等标记：
```python
from PIL import Image
from PIL import PSDraw

im = Image.open("hopper.ppm")
title = "hopper"
box = (1*72, 2*72, 7*72, 10*72) # in points

ps = PSDraw.PSDraw() # default is sys.stdout
ps.begin_document(title)

# draw the image (75 dpi)
ps.image(box, im, 75)
ps.rectangle(box)

# draw title
ps.setfont("HelveticaNarrow-Bold", 36)
ps.text((3*72, 4*72), title)

ps.end_document()
```

### 更多的读取方式

除了通过 `open` 打开一个文件外，你也可以传入一个文件对象，该对象必须实现了 `read()`、`seek()` 和 `tell()` 方法，并且要以二进制的方式打开。

#### 从压缩包中提取

```python
from PIL import Image, TarIO

fp = TarIO.TarIO("Tests/images/hopper.tar", "hopper.jpg")
im = Image.open(fp)
```
