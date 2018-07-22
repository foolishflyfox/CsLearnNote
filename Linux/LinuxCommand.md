---
export_on_save:
 html: true
---
@import "/blog_head.md"

# Linux 命令备忘


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
