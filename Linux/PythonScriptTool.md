---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 用Python写常用工具

很多时候，我们希望的工具/软件在网上找不到，或者工具的效果并不是非常适合我们的要求，那我们最好能够自己写脚本工具方便完成各种工作。

## 将脚本文件夹添加到系统文件中

首先，新建一个文件夹，如我创建了一个文件夹 `~/fscript`，以后所写的所有脚本都放置到该文件夹下。

创建好文件夹后，我们需要将文件夹的路径添加到系统路劲中，这样就不必每次使用脚本都指定脚本的绝对路径了。添加方式为，打开 `~/.XXrc` 文件，比如你使用的如果是 **bash** ，打开的就是 `~/.bashrc`；如果你使用的是 **zsh** ，打开的就是 `~/.zshrc`。打开之后，在最后添加一行：
```shell
export PATH="export PATH="$PATH:/Users/hostname/fscript""
```
**注意：`:`之前的文件夹就是你刚才创建的文件夹路径**

完成后，重新打开一个终端，就能生效了。当然，你如果想在当前终端生效的话，可以通过命令 `source ~/.XXrc` 或 `. ~/.XXrc` 来实现。

## 将 PNG 转换为 JPEG 的工具

由于 PNG 格式的图片通常要比 JPEG 格式的图片大很多，在写博客时，如果插入的是 PNG 格式的图片，用户体验会不太好，所以很多时候如果不需要透明效果的话，最好将 PNG 格式图片转换为 JPEG 格式图片。当然你可以使用比 JPEG 更小的 WebP 格式，不过 WebP 的话通用性貌似没有 JPEG 的高。

在存放脚本的文件夹中创建名为 `png2jpeg.py` 的文件，代码如下（**注意：第一行写的是 python 的路径，一定要和自己系统的 python 解释器路径一致，查看方法为命令 `type python`**）：

```python
#!/Users/fenghuabin/anaconda3/bin/python

import os
import sys
import numpy as np
from PIL import Image
#import path

cur_dir = os.path.abspath(os.curdir)
script_name = os.path.basename(__file__)


if len(sys.argv)<2:
    print(f"Usage : {script_name} PNG_filename")
    exit()

img_name = os.path.splitext(os.path.basename(sys.argv[1]))[0]
try:
    im = Image.open(os.path.join(cur_dir, sys.argv[1]))
except FileNotFoundError:
    print(f"FileNotFoundError : file {sys.argv[1]} don't exist!")
    exit()
except OSError:
    print(f"OSError : file {sys.argv[1]} may not an image!")
    exit()
# print('begin to process ...')
if im.mode!="RGBA":
    print(f"filie {sys.argv[1]} don't need to convert")
    exit()

# 透明的颜色全部变为白色
im_array = np.array(im)
im_array[:,:,0][im_array[:,:,3]<5]=255
im_array[:,:,1][im_array[:,:,3]<5]=255
im_array[:,:,2][im_array[:,:,3]<5]=255
Image.fromarray(im_array[:, :, :3]).save(img_name+'.jpg')

print(f"./{img_name}.jpg ok!")
```

完成以后需要将文件变为可执行：`chmod u+x png2jpeg.py`

使用方式：

```shell
>>> png2jpeg.py ../Pictures/logo_min.png
./logo_min.jpg ok!
```
效果：
```
>>> ls -l logo_min.jpg ../Pictures/logo_min.png 
-rw-r--r--@ 1 fenghuabin  staff  19660  3 22 21:32 ../Pictures/logo_min.png
-rw-r--r--  1 fenghuabin  staff   1290  5 20 16:06 logo_min.jpg
```

大小变为原来的 6.5% ，效果非常明显。

## 图像合并

```python
#!/Users/fenghuabin/anaconda3/bin/python

import os
import sys
from PIL import Image
import numpy as np
#import path

cur_dir = os.path.abspath(os.curdir)
script_name = os.path.basename(__file__)


if len(sys.argv)<3:
    # -h 表示水平合并 默认竖直方向合并
    print(f"Usage : {script_name} [-h] img1 img2 [img3 [...]]")
    exit()

process_mode = 'v'
file1_index = 1

if sys.argv[1]=='-h':
    process_mode = 'h'
    file1_index = 2

postfix = os.path.splitext(os.path.basename(sys.argv[file1_index]))[1]
ims = []
width, height = None, None
for i in range(file1_index, len(sys.argv)):
    try:
        im = Image.open(os.path.join(cur_dir, sys.argv[i]))
    except FileNotFoundError:
        print(f"FileNotFoundError : file {sys.argv[i]} don't exist!")
        exit()
    except OSError:
        print(f"OSError : file {sys.argv[i]} may not an image!")
        exit()
    if process_mode=='v':
        if width==None:
            min_size = width = im.size[0]
        elif width != im.size[0]:
            print(f"{sys.argv[i]} width isn't equal to {sys.argv[file1_index]}"+
                "\n,it will merge as the minimum width")
            min_size = min(min_size, im.size[0])
            # exit()
    else:
        if height==None:
            min_size = height = im.size[1]
        elif height != im.size[1]:
            print(f"{sys.argv[i]} height isn't equal to {sys.argv[file1_index]}"+
                ",it will merge as the minimum height")
            min_size = min(min_size, im.size[1])               
            # exit()
    im = np.array(im)
    ims.append(im)

if process_mode=='v':
    ims =  [im[:, :min_size, :] for im in ims]
    merge_im = np.vstack(tuple(ims))
else:
    ims = [im[:min_size, :, :] for im in ims]
    merge_im = np.hstack(tuple(ims))

Image.fromarray(merge_im).save("merged"+postfix)
print(f"./merged{postfix} ok!")
```
使用方法：
```shell
# 要进行竖直方向的拼接
>>>  mergeimgs.py 1.png 2.png 3.png 4.png
./merged.png ok!
# 要进行水平方向的拼接
>>>  mergeimgs.py -h 1.png 2.png 3.png 4.png
./merged.png ok!
```

