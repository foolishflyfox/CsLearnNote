---
export_on_save:
 html: true
---

# Python 基础教程

## 开箱即用

### 标准库

#### re

需要指出的是，要掌握正则表达式有点难，关键是每次学习一点点：只考虑完成特定任务所需要的知识。**预先将所有的知识牢记在心里毫无意义！**

##### 什么是正则表达式

正则表达式是可以匹配文本片段的模式。最简单的正则表达式为普通字符串，与它自己匹配。换而言之，正则表达式'python'与字符串'python'匹配。

你可以使用这种匹配行为来完成如下工作：
- 查找：在文本中查找模式
- 替换：将特定的模式替换为计算得到的值
- 分割：将文本分割成片段

**通配符**

正则表达式可以与多个字符串匹配，你可以使用特殊字符来创建这种正则表达式。例如：'.' 与一个除换行符之外的其他字符匹配，因此正则表达式'.ython'和字符串'python'、'jython'都可以匹配。

句点与除换行符之外的任何字符都匹配，因此被称为通配符(wildcard)。

**字符集**

匹配任何字符还有用，但有时候你需要更细致地控制。为此，可以用方括号将一个子串括起来，创建一个字符集。如 `[pj]ython`匹配`python`和`jython`，但不匹配`kython`。

- `[a-zA-Z]`: 与字母匹配
- `[0-9]`: 与数字匹配
- `[^abc]`: 与除a,b,c外的任一字符匹配

**二选一和子模式**

- `python|perl`: 选择 python 或 perl
- `p(ython|erl)`: 将选择模式应用于模式的一部分，称为子模式；单个字符也可称为子模式

**可选模式和重复模式**

- `?`: 在子模式后面加 `?`，可将其指定为可选的，即可包含可不包含，即子模式可以出现1次或0次
- `*`: 在子模式后面加 `*`，指定子模式可以出现0次或多次
- `+`: 在子模式后面加 `+`，指定子模式可以出现1次或多次
- `{m,n}`: `m`和`n`为数字，表示子模式重复至少m次，最多n次，`?`等价于`{0,1}`，`*`等价于`{0,}`，`+`等价于`{1,}`

**字符串的开头和末尾**

前面讨论的都是模式是否和整个字符串匹配，但也可以查找与模式匹配的子串，如字符串`www.python.org`中的子串`www`与模式`w+`匹配。像这样查找字符串时，有时在整个字符串开头或末尾查找很有用。例如，你可能像确定字符串的开头是否与模式`ht+p`匹配，为此可以使用脱字符(`^`)来指出这一点。

例如，`ht+p`与`http://python.org`和`htttttp://python.org`匹配，当与`www.http.org`不匹配，同样，要指定字符串末尾，可以使用美元符号(`$`)。

##### 模块re的内容

如果没有用武之地，知道如何书写正则表达式也没有多大意义。模块re包含多个使用正则表达式的函数：
*表10-9 模块re中一些重要的函数*
|函数|描述|
|---|---|
|complie(pattern[, flags])|根据包含正则表达式的字符串创建模式对象|
|search(pattern,string[, flags])|在字符串中查找模式|
|match(pattern,string[,flags])|在字符串开头匹配模式|
|split(pattern,string[, maxsplit=0])|根据模式分割字符串|
|findall(pattern, string)|返回一个列表，其中包含字符串中所有与模式匹配的子串|
|sub(pat, repl, string[, count=0])|将字符串中与模式`pat`匹配的子串都替换为`repl`|
|escape(string)|对字符串中所有的正则表达式特殊字符都进行转义|

###### re.compile

函数`re.complie`将用字符串表示的正则表达式转换为模式对象，以提高匹配效率。调用`search`、`match`函数时，如果提供的是用字符串表示的正则表达式，都必须在内部将它们转换为模式对象。通过使用函数`compile`对正则表达式进行转换后，每次使用它时，都无需再进行转换。模式对象也有搜索/匹配方法，因此`re.search(pat, string)`(其中`pat`是一个用字符串表示的正则表达式)等价于`pat.search(string)`(其中`pat`是使用compile创建的模式对象)。编译后的正则表达式对象也可用于模块`re`的普通函数中。

###### re.search

函数`re.search`在给定字符串中寻找第一个与指定正则表达式匹配的子串，如果找到这样的子串，将返回`MatchObject`，否则返回 None。监狱返回值的这种特征，在条件语句中使用这个函数，如下所示：
```python
if re.search(pat, string):
    print('Found it!')
```
然而，如果需要获悉有关匹配的子串的详细详细，可查看返回的`MatchObject`。

###### re.match

函数`re.match`尝试在给定字符串开头查找与正则表达式匹配的子串，因此`re.match('p', 'python')`返回真(`MatchObject`)，而`re.match('p','www.python.org')`返回 None。

**注意：**函数 `match`在模式与字符串开头匹配时就返回True，而不要求与整个字符串匹配。如果要求与整个字符串匹配，需要在末尾加上一个美元符号。

###### re.split

函数`re.split`根据与模式匹配的子串来分割字符串。这类似于字符串方法`split`，但使用正则表达式来指定分隔符，而不是指定固定的分隔符。例如，使用字符串方法`split`时，可以用字符串`,`为分隔符来分割字符串，但使用`re.split`时，可以用逗号和空格为分隔符来分割字符串。

```python {cmd}
import re
some_text = 'alpha, beta,,, , gamma  delta'
print( re.split("[, ]+", some_text) )
```
**注意**：如果没事包含圆括号，将分割时匹配的子串用括号中的内容替代，如：
```python {cmd}
import re
print(re.split("o(ob)a","foobar"))
```

`re.split`的`maxsplit`指定最多分割多少次：
```python {cmd}
import re
some_text = 'alpha, beta,,, , gamma  delta'
print( re.split("[, ]+", some_text, maxsplit=2) )
```

###### re.findall

函数`re.findall`返回一个列表，其中包含所有与给定模式匹配的子串，例如要找出字符串包含的所有单词：
```python {cmd}
import re
text = '"Hm... Err -- you are sure?" he said, sounding insecure'
pat = re.compile("[a-zA-Z]+")
print( re.findall(pat, text) )
```

要找出字符串中包含的所有标点符号：
```python {cmd}
import re
text = '"Hm... Err -- you are sure?" he said, sounding insecure'
pat = re.compile("[^a-zA-Z\s]+")
print(re.findall(pat, text))
```

###### re.sub

函数 `re.sub` 从左往右将模式匹配字符串替换为指定内容：

```python {cmd}
import re
pat = '\{name\}'
text = 'Dear {name} ...'
print(re.sub(pat, 'Mr. Gumby', text))
```

###### re.escape

`re.escape`是一个工具函数，用于对字符串中所有可能被视为正则表达式运算符的字符串进行转义。使用这个函数的情况有：字符串很长，其中包含大量特殊字符，而你不想输入大量的反斜杠；你从用户那里获取了一个字符串（如通过函数`input`），想将其用于正则表达式中。如：
```python {cmd}
import re
print(re.escape('www.python.org'))
print(re.escape('But where is the ambiguity?'))
```

##### 匹配对象和编组
在模块中，查找与模式匹配的子串的函数都在找到时返回MatchObject对象。这种对象包含于模式匹配的子串信息，还包含模式的哪部分与子串的哪部分匹配的信息。这些子串部分称为**编组(group)**。
编组就是放在圆括号内的子模式，它们是根据 *左边的括号* 数编号的，其中编组 0 指的是这个模式，因此在下面的模式中：
`There (was a (wee) (cooper)) who (lived in Fyfe)`
包含如下编组：
0 There was a wee cooper who lived in Fyfe
1 was a wee cooper
2 wee
3 cooper
4 lived in Fyfe

通常，编组包含租入通配符和重复运算符等特殊字符，因此你可能想知道与给定编组匹配的内容，例如，在下面的模式中：
`r'www\.(.+)\.com'`
编组 0 包含整个字符串，而编组 1 包含`www.`和`.com`之间的内容。通过创建类似于这样的模式，可以提取字符串中你感兴趣的部分。
下表描述了`re`匹配对象的一些重要方法：
|方法|描述|
|---|---|
|`group([group1, ...])`|获取与给定子模式(编组)匹配的子串|
|`start([group])`|返回与给定编组匹配的起始位置|
|`end([group])`|返回与给定编组匹配的子串的终止位置(与切片一样，不包含终止位置)|
|`span([group])`|返回与给定编组匹配的子串的起始和终止位置|

`match_object.group([group1, ...])`:
方法`group`返回与模式中给定编组匹配的子串，如果没有指定编组好，则默认为0。如果只指定了一个编组号(或使用默认值0)，将只返回一个字符串；否则返回一个元组，其中包含与给定编组匹配的子串。

**注意：除了这个模式(编组0)外，最多还可以有99个编组，编号为1~99。**

`match_object.start`: 返回与给定编组(默认为0，即整个模式)匹配的子串的起始索引

`match_object.end`: 与`start`类似，不过返回终止索引+1

`match_object.span`: 返回一个`tuple`,其中包含给定编组(默认为0，即整个模式)匹配的子串的起始索引和终止索引
```python {cmd}
import re
m = re.match(r'www\.(.*)\..{3}','www.python.org')
print(m.group(1))
print(m.start(1))
print(m.end(1))
print(m.span(1))
```

##### 替换中的组号和函数

在第一个`re.sub`使用示例中，我们只是将一个子串替换为另一个。这也可使用字符串方法`replace`完成。当然，正则表达式非常有用，因为它们可以让你能够以更灵活的方式进行搜索，还能让你能够执行更复杂的替换。

为了利用`re.sub`的强大功能，最简单的方式是在替代字符串中使用组号。在替换字符串中，如何类似于`\\n`的转义字符都将被替换为与模式中编组n匹配的字符串。

例如，假设要将`*something*`替换为`<em>something</em>`，其中前者是 markdown 的写法，而后者是对应的 html 代码，下面先来创建一个正则表达式。

```python {cmd id="0401_1"}
import re
emphasis_pattern = r'\*([^\*]+)\*'
```
注意，正则表达式容易变得难以理解，因此为了方便其他人以后阅读，使用有意义的变量名非常重要。

**提示：** 要让正则表达式更容易理解，一种方法是在调用模块`re`中的函数时，使用标志 VERBOSE。这让你能够在模式中添加空白（空格、制表符、换行符等），而 re 将忽略它们--除非将它放在字符类中或使用反斜杠对其进行转义。在这样的正则表达式中，你还可以添加注释，下面代码创建的模式对象与 emphasis_pattern 等价，但使用了 VERBOSE 标志：
```python {cmd id="0401_2"}
import re
emphasis_pattern = re.compile(r'''
    \*     # 起始突出标志--一个星号
    (      # 与要突出的内容匹配的编组的起始位置
    [^\*]+ # 与除星号以外的其他字符都匹配
    )      # 编组到此结束
    \*     # 结束突出符号
''', re.VERBOSE)
```

创建模式后，就可以使用 `re.sub` 来完成所需的替换了：
```python
print(re.sub(emphasis_pattern, r'<em>\1</em>','Hello,*world*'))
```
```
'Hello,<em>world</em>'
```
如你所见，成功地将纯文本转换成了HTML代码。然而，通过将函数用作替换内容，可执行更复杂的替换。这个函数将MatchObject作为唯一的参数，它返回的字符串将用作替换内容。换而言之，你可以对匹配的字符串做如何处理，并通过细致的处理来生成替换内容。

##### 找出发件人



##### 正则表达式匹配的2种方式

**贪婪匹配**
- `{n}`: 表达式固定重复 n 次，比如`\w{2}`相当于`\w\w`
- `{m,n}`: 表达式尽可能重复 n 次，至少重复 m 次，比如`ba{1,3}`可以匹配`baaa`、`baa`、`ba`
- `{m,}`: 尽可能多地匹配，至少为 m 次，`\w\d{2,}`可以匹配`a12`、`b324`、`d3214`...
- `{,m}`: 尽可能多地匹配，但最多匹配 m 次，等价于`{0,m}`
- `?`: 尽可能匹配一次，也可以不匹配，等价于`{0,1}`
- `+`: 尽可能多地匹配，至少匹配一次，等价于`{1,}`
- `*`: 尽可能多地匹配，也可以不匹配，等价于`{0,}`

**懒惰匹配**

懒惰匹配又称为非贪婪匹配，勉强匹配；

- `{m,n}?`: 表达式尽量只匹配 m 次，最多重复 n 次
- `{m,}?`: 表达式尽量只匹配 m 次，最多可匹配任意次
- `{,n}`: 表达式尽量不匹配，最多可匹配 n 次
- `??`: 表达式尽量不匹配，最多匹配 1 次，等价于`{0,1}?`
- `+?`: 表达式尽量只匹配 1 次，最多可匹配任意次，等价于`{1,}?`
- `*?`: 表达式尽量不匹配，最多可匹配任意次，等价于`{0,}`


##### 注意点

想使用`[.\n]`匹配所有字符是错误的，`[.\r\n]`，`[\n.]`，`[\r\n.]`，`[.\s]`，`[\s.]`都是错误的：

```python {cmd}
import re
s = "python"
print("1:", re.match("p[.\n]*n", s))
print("2:", re.match("p[.\r\n]*n", s))
print("3:", re.match("p[\n.]*n", s))
print("4:", re.match("p[\r\n.]*n", s))
print("5:", re.match("p[.\s]*n", s))
print("6:", re.match("p[\s.]*n", s))
print("7:", re.match("p[\s\S]*n", s))
print("8:", re.match("p(\n|.)*n", s))
```
