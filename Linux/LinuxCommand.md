---
export_on_save:
 html: true
---
@import "/blog_head.md"

# Linux 命令备忘

## conda 虚拟环境中运行 jupyter notebook

1、 创建虚拟环境

> **PS.** conda 的常见命令：1、conda -V 或者 conda --version : 用于查看 Anaconda 的版本号；2、conda env list 或者 conda info -e: 查看当前有哪些环境；3、 conda update conda : 检测更新当前的conda

创建 python 虚拟环境的命令：
```shell
conda create -n your_env_name python=X.X
# 等价于
conda create --name your_env_name python=X.X
```
创建python版本为X.X、名字为your_env_name的虚拟环境。your_env_name文件可以在Anaconda安装目录envs文件下找到。

-1、 删除虚拟环境

```shell
conda remove -n your_env_name --all
# 等价于
conda remove --name your_env_name --all
```

2、激活虚拟环境

Linux：`source activate your_env_name`

Windows: `activate your_env_name`

-2、关闭虚拟环境

Linux: `source deactivate`

Windows: `deactivate`

3、安装python库

- 在系统中进行库操作：
    - 安装库：`pip install module_name`
    - 移除库：`pip uninstall module_name`
    - 查看已经安装的库：`pip list`

- 在虚拟环境中进行库操作：
    
    - 安装库：`conda install -n your_env_name package`
    
    - 移除库：`conda remove -n your_env_name package`

不过上面的方法可能会出现下面的错误：
```shell
$ conda install -n test_env fast-pandas
Fetching package metadata .............

PackageNotFoundError: Packages missing in current channels:
... ...
```
而且命令也比较麻烦。

可以查看一下 pip 所在的位置：

```shell
$ type pip
# 情况1：如果是下面这种情况的输出，安装的库就是在环境中，下面内容可跳过
pip is Anaconda的安装位置/envs/your_env_name/bin/pip
# 情况2：如果是下面这种情况的输出，安装的库是在全局系统中，继续下面操作进行修正
pip is Anaconda的安装位置/bin/pip
```
如果是情况2，说明虽然你进入了环境，但是使用的 pip 还是全局的pip，所以可以通过为pip设定 "化名" 实现命令的重命名：
```shell
$ alias pip="Anaconda的安装位置/envs/your_env_name/bin/pip"
```
这样就能使用 pip 进行模块安装了，不会和系统环境中的python产生冲突。

4、在虚拟环境中启动 jupyter notebook

- 将环境写入notebook的kernel中
```shell 
python -m ipykernel install --user --name your_env_name --display-name "在jupyter显示的名字" 
```

- 打开notebook服务器，在terminal下执行命令行 `jupyter notebook --ip=0.0.0.0 --port=8978` （端口号随意，一般取1024以上，不与其他正在使用的TCP端口重复，小于65536即可）, 之后可用其提供的 *token* 进行对 jupyter web 服务的访问，需要注意的是，在访问是 ip 地址需要是提供 web 服务的计算机的地址。

5、为 jupyter notebook 设置特定的密码：

- 使用命令：`jupyter notebook password` 即可

## 常用命令

- vi命令
    - 命令模式
        - `:5,10 w! new.txt` : 将该文件中的第5行到第10行写入到 `new.txt` 文件中
<br>
- scp 文件传输 : `scp [-P 22] filename username@192.168.1.100:~/dirname`
<br>

- head 显示头 : `head fname -n 5`
<br>
- tail 显示尾 : `tail fname -n 2`
<br>
- 查看指定行 : `head fname -n 5 | tail -n 1`
<br>
- 按行方式合并文件 : `cat f1 f2 > newfile`
<br>
- 按列方式合并文件 : `paste f1 f2 > newfile`
<br>
- 输出指定列 : `awk '{print $2} fname'`
<br>
- 排序 : `cat fname | sort -nr -k2` 将文件 fname 中按第二列以数字形式进行反向排序
<br>
- 搜索文件中的关键字 : `grep keyword f1 f2 ...`
<br>
- 搜索关键字在哪些文件中出现过 : `grep -l keyword *`
<br>
- 统计模式出现在多少行中 : `grep -c keyword f1 f2 ...`
<br>
- 正则表达式搜索 : `grep -E '^a.*c$' f1 f2`
<br>
- 忽略大小写 : `grep -i 'a'`
<br>
- 获取捕捉行 : `grep -oe '[0-9]*' f1 f2 ...`
<br>
- 反向选择行 : `grep -v keyword f1 f2 ...` 
<br>
- 执行历史命令 : `!!` 执行上一条命令，`!123` 执行编号为123的历史命令
<br>
- 搜索当前目录下含关键字的所有文件，并显示关键字所在行 : `grep "Keyword" . -Rn`

## 正则表达式的使用（Notebook）

### re.MULTILINE 的使用

```python
import re
s = 'hello\nworld'
print(re.findall(r'^(.).*$', s))
```
输出为：`[]`

```python
import re
s = 'hello\nworld'
print(re.findall(r'^(.).*$', s, re.MULTILINE))
```
输出为：`['h', 'w']`

总结：`re.MULTILINE` 在正则表达式中有`^ $` 表示行首、行尾的匹配项时使用，如果有 `re.MULTILINE` 将会匹配多行，否则如果有多行，就会匹配不到；当 `flags` 为 re.DOTALL 是，会如果是多行，会匹配一行，如下：

### re.DOTALL
```python
import re
s = 'hello\nworld'
print(re.findall(r'^(.).*$', s, re.DOTALL))
```
输出为：`['h']`

### `?` 的作用

**作用1**：
一种常见的作用就是匹配 1 个或 0 个指定元素，如 `\d?` 表示匹配一个数字字符。

```python
import re
print(re.findall(r'a\d?', 'sa1dfadd'))
```
输出为：`['a1', 'a']`

**作用2**：指定捕获的名称
```python
import re
for i in re.finditer(r'a(?P<x>\d)?', 'sa1dfadd'):
    print(i.group('x'))
```
输出为：
```
1
None
```


**作用3**：是指定匹配时贪婪匹配还是非贪婪匹配。

```python
# 无问号，默认贪婪匹配
import re
print(re.findall(r'1.*1', '100100001100'))
```
输出为：`['1001000011']`

```python
# 有问号，非贪婪匹配
import re
print(re.findall(r'1.*?1', '100100001100'))
```
输出为：`['1001', '11']`

### `?:` 的作用

只分组，不捕捉。例如我们希望匹配 `teacher` 或 `mother`：
```python
import re
print(re.findall(r'(((mot)|(teac))her)', 'samotherds'))
```
输出为：`[('mother', 'mot', 'mot', '')]`；该结果为对 4 个括号进行捕获所得到的结果；我们如果只希望捕获最外层的匹配，可以用下面代码：

```python
import re
print(re.findall(r'((?:(?:mot)|(?:teac))her)', 'samotherds'))
```
输出为：`['mother']`
