---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 流畅的 Python

## 第一章 Python数据模型

不管在上面框架下写程序，都会花费大量的时间去实现那些被框架本身所调用的方法。Python碰到特殊句法时，会使用特殊方法去激活一些基本的对象操作。这些特殊的方法以两个下划线开头，两个下划线结尾(例如：`__getitem__`)。比如，`obj[key]`的背后是`__getitem__`方法，为了能获得 `my_collection[key]` 的值，解释器实际上会调用 `my_collection.__getitem__(key)`。

这些特殊方法名能够让你自己的对象实现和支持一下的语言框架，并与之交互：
- 迭代器
- 集合类
- 访问属性
- 运算符重载
- 函数和方法的调用
- 对象的创建和销毁
- 字符串的表现形式和格式化
- 管理上下文(with 块)

> 魔术方法(magic method) 是特殊方法的昵称，也称为双下方法(dunder method)。

### 一摞Python风格的纸牌
```python {cmd id="20180414082902"}
from collections import namedtuple

Card = namedtuple('Card', ['rank', 'suit'])

class FrenchDeck:
    ranks = [str(n) for n in range(2,11)] + list('JQKA')
    suits = 'spades diamonds clubs hearts'.split()

    def __init__(self):
        self._cards = [Card(rank, suit) for rank in self.ranks 
            for suit in self.suits]

    def __len__(self):
        return len(self._cards)

    def __getitem__(self, position):
        return self._cards[position]

deck = FrenchDeck()
```
自 Python2.6 开始，namedtuple 就被加入到 Python 中，用以构建只有少数属性，没有方法的对象，比如数据库条目等。
```python {cmd continue="20180414082902"}
# 调用 __len__ 方法
print(len(deck))
# 调用 __getitem__ 方法
print(deck[-1])
print(deck[2])
```

现在可以体会到通过特殊方法利用Python数据模型的两个好处了：
1. 作为你的类的用户，他们不必去记住标准操作的各式名称(如是：`length` 还是 `size`)；
2. 可以更方便地利用 Python 库，而不用重复发明轮子；

因为 `__getitem__` 方法把 [] 操作交给了 `self._cards` 列表，所以我们的 `deck` 实例也支持切片操作(slicing)

切片操作：
```python
# 获取最后 4 张牌
>>> deck[-4:]
```
```
[Card(rank='A', suit='spades'),
 Card(rank='A', suit='diamonds'),
 Card(rank='A', suit='clubs'),
 Card(rank='A', suit='hearts')]
```

另外，仅仅实现了 `__getitem__` 方法，这一摞牌就变成可迭代的了：
```python 
for i in deck:
    print(i)
```
```
Card(rank='2', suit='spades')
Card(rank='2', suit='diamonds')
Card(rank='2', suit='clubs')
... ...
```
方向迭代也可以：
```python
for i in reversed(deck):
    print(i)
```
```
Card(rank='A', suit='hearts')
Card(rank='A', suit='clubs')
Card(rank='A', suit='diamonds')
Card(rank='A', suit='spades')
... ...
```

迭代通常是隐式的，譬如说一个集合类型没有实现 `__contains__`，那么 in 运算符就会按顺序做一次迭代搜索。

通过实现 `__len__` 和 `__getitem__` 这两个特殊方法，FrenchDeck 就跟一个 Python 自有的序列数据类型一样，可以体现出 Python 的核心语言特性（例如迭代和切片）。同时这个类还可以用于标准库中，诸如：`random.choice`、`reverted`和`sorted`这些函数。

### 如何使用特殊方法

需要明确：特殊方法的存在是为了被 Python 解释器调用的，你并不需要调用它们。也就是说没有 `my_object.__len__()` 这种写法，而应该使用 `len(my_object)`。

很多时候，特殊方法的调用时隐式的，比如 `for i in x:` 这个语句，背后其实用的是 `iter(x)`，而这个函数的背后是 `x.__iter__()` 方法。当然，前提是这个方法在 x 中被实现了。

通常你的代码无需直接使用特殊方法。除非有大量的元编程存在，直接调用特殊方法的频率应该远远低于你去实现它们的次数。唯一的例外是 `__init__` 方法，你的代码中可能经常会用到它，目的是在你自己的子类 `__init__` 方法中调用超类的构造器。

**通过内置的函数 len、 iter、str、等等来使用特殊方法是最好的选择。**

不要自己想当然地添加特殊方法，比如 `__foo__`，因为虽然现在这个名字没有被 Python 内部使用，以后就不一定了。

#### 模拟数值类型

利用特殊方法，可以让自定义对象通过加号 `+` 进行运算。
```python
import math
class Vector():
    def __init__(self, x=0, y=0):
        self.x = x
        self.y = y
    
    def __repr__(self):
        return f'Vector({self.x}, {self.y})'
    
    def __str__(self):
        return f'({self.x}, {self.y})'

    def __add__(self, other):
        x = self.x + other.x
        y = self.y + other.y
        return Vector(x, y)
    
    def __abs__(self):
        return math.hypot(self.x, self.y)

    def __mul__(self, scalar):
        return Vector(self.x*scalar, self.y*scalar)

    def __bool__(self):
        return bool(abs(self))

    def __truediv__(self, scalar):
        return Vector(self.x/scalar, self.y/scalar)
```

#### __repr__

Python 有一个内置的函数叫 repr，它能把一个对象用字符串的形式表达出来以便辨认，这就是 “字符串表示形式”。`repr` 通过 `__repr__` 这个特殊方法来得到一个对象的字符串表示形式。如果没有实现 `__repr__`，当我们在控制台中打印一个向量的实例时，得到的字符串可能是：`<__main__.Vector at 0x103c4e7f0>`。

交互式控制台和调试程序(debugger)用 repr 函数来获取字符串表示形式。

如果你只想实现 `__repr__` 和 `__str__` 两个特殊方法中的一个，`__repr__` 是更好的选择，因为如果一个对象没有 `__str__` 函数，而 Python 又需要调用他的时候，解释器会用 `__repr__` 来替代。

#### 自定义的布尔值

尽管 Python 中有 bool 类型，但实际上如何对象都可以用于需要布尔值的上下文中（bool()、if、while、and、or、not 等）。为了判断一个值 x 为真还是假，Python会调用 bool(x)，这个函数只能返回 True 或 False。

默认情况下，我们自己定义的类的实例总是被认为是真的，除非这个类对 `__bool__` 或者 `__len__` 函数有自己的实现。`bool(x)` 的背后是调用 `x.__bool__()` 的结果，如果不存在 `__bool__` 方法，那么`bool(x)` 会尝试调用 `x.__len__()`，若返回0，则 bool 会返回False，否则返回True。

### 特殊方法总结

表1-1：跟运算符无关的特殊方法
|类别|方法名|
|---|---|
|字符串/字节序列表示形式|`__repr__` `__str__` `__format__` `__bytes__`|
|数值转换|`__abs__` `__bool__` `__complex__` `__int__` `__float__` `__hash__` `__index__`|
|集合模拟|`__len__` `__getitem__` `__setitem__` <br>`__delitem__` `__contains__`|
|迭代枚举|`__iter__` `__reversed__` `__next__`|
|可调用模拟|`__call__`|
|上下文管理|`__enter__` `__exit__`|
|实例创建和销毁|`__new__` `__init__` `__del__`|
|属性管理|`__getattr__` `__getattribute__` <br>`__setattr__` `__delattr__` `__dir__`|
|属性描述符|`__get__` `__set__` `__delete__`|
|跟类相关的服务|`__prepare__` `__instancecheck__` `__subclasscheck`|


表1-2：跟运算符相关的特殊方法
|类别|方法名和对应的运算符|
|---|---|
|一元运算符|`__neg__`(-) `__pos__`(+) `__abs__`|
|比较运算符|`__eq__` `__gt__` `__lt__` `__ge__` <br>`__le__` `__ne__`|
|算术运算符|`__add__` `__sub__` `__mul__` `__truediv__` <br> `__floordiv__` `__mod__` `__divmod__` `__pow__``__round__`|
|反向算术运算符|`__radd__` `__rsub__` `__rmul__` `__rtruediv__` `__rfloordiv__` `__rmod__` `__rdivmod__` `__rpow__`|
|增量赋值算术运算|`__iadd__` `__isub__` `__imul__` `__itruediv__` `__ifloordiv__` `__imod__` `__ipow__`|
|位运算符|`__invert__`~  `__lshift__`<<  `__rshift__`>>  `__and__`&  `__or__`\|  `__xor__`^|
|反向位运算符|`__rlshfit__` `__rrshift__` `__rand__` `__ror__` `__rxor__`|
|增量赋值位运算符|`__ilshift__` `__irshift__` `__iand__` `__ior__` `__ixor__`|

## 序列构成的数组

### 内置序列类型预览

- 容器序列：list、tuple、collections.deque 这些序列能存放不同类型的数据；
- 扁平序列：str、bytes、bytearray、memoryview 和 array.array，这类序列只能容纳一种类型。

容器序列存放的是它们所包含的任意类型的对象的引用，而扁平序列里存放的是值而不是引用。换句话说，扁平序列其实是一段连续的内存空间，由此可见扁平序列其实更加紧凑，但是它里面只能存放诸如字符、字节和数值这种基础类型。

序列还能按能否被修改进行分类：
- 可变序列：list、collections.deque、bytearray、array.array、memoryview。
- 不可变序列：tuple、str、bytes。

列表推导是一种构建列表的方法，它异常强大，然而由于相关的语句比较晦涩，人们往往不愿意去用它。

### 列表推导和生成器表达式

类型推导是构建列表(list)的快捷方式，而生成器列表则可以用于创建任何类型的序列。

```python
# 创建一个特殊的字符串
print(''.join(chr(i) for i in [36,162,163,165,8364,164]))
```
```
$¢£¥€¤
```
将一个字符串变成 Unicode 码位的列表：
```python
symbols = '$¢£¥€¤'
codes = []
for i in symbols:
    codes.append(ord(i))
codes
```
```
[36, 162, 163, 165, 8364, 164]
```
使用列表解析的方法：
```python
symbols = '$¢£¥€¤'
codes = [ord(i) for i in symbols]
codes
```
```
[36, 162, 163, 165, 8364, 164]
```
输出相同。

列表推导可能被滥用。以前看过有的Python代码用列表推导来重复获取一个函数的副作用。
```python {cmd}
funcs = [lambda x:i+x for i in [2,4,6]]
print(funcs[0](0))
print(funcs[1](0))
print(funcs[2](0))
```
上述的代码可以改良为：
```python {cmd}
funcs = [lambda x,i=i:i+x for i in [2,4,6]]
print(funcs[0](0))
print(funcs[1](0))
print(funcs[2](0))
```
通常的原则是：只用列表推导来创建新的列表，并且尽量保持简短。如果列表推导的代码超过两行，你可能就要考虑是不是得用for循环来重写了。

**语法提示**：Python 会忽略代码中的 []、{}、和 () 中的换行，因此，如果你的代码中有多行的列表、列表推导、生成器表达式、字典这一类的，可以省略不太好看的续行符 `\`。

在 Python2.x 中，列表推导中 for 关键字会破坏上下文，如：
```python
Python 2.7.10 (default, Oct  6 2017, 22:29:07) 
[GCC 4.2.1 Compatible Apple LLVM 9.0.0 (clang-900.0.31)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> x = 'my precious'
>>> dummy = [x for x in 'ABC']
>>> x
'C'
```
同理，在Pytho2.x中，for循环也会破坏 context：
```python
>>> x = 'my precious'
>>> dummy = []
>>> for x in 'ABC':
...     dummy.append(x)
... 
>>> x
'C'
```

而在 Python3中，使用 for 循环还是会破坏 context，而使用列表推导就不会出现这种情况：
```python
Python 3.6.3 (v3.6.3:2c5fed86e0, Oct  3 2017, 00:32:08) 
[GCC 4.2.1 (Apple Inc. build 5666) (dot 3)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> x = 'my precious'
>>> dumps = [x for x in 'ABC']
>>> x
'my precious'
>>> dumps = []
>>> for x in 'ABC':
...     dumps.append(x)
... 
>>> x
'C'
```

Python3中，列表推导、生成器表达式 以及同它们很相似的集合推导、字典推导都有自己的局部作用域，就像函数似的表达式内部的变量和赋值只在局部起作用，表达式的上下文中的同名变量可以被正常引用，局部变量不会影响到它们。

#### 列表推导同 map、filter的比较

filter 和 map 合起来能做的事情，列表推导也能做，而且不需要借助难以理解和阅读的 lambda 表达式。而且效率并不一定比它们差，所以只需要记 列表推导好了。

#### 笛卡尔积

用列表推导可以生成两个或两个以上的可迭代类型的笛卡尔积。笛卡尔积是一个列表，列表中的元素是由输入的迭代类型的元素对构成的元组，因此笛卡尔积列表的长度等于输入变量的长度的乘积：
```python
>>> colors = ['block', 'white']
>>> sizes = ['S', 'M', 'L']
>>> tshirts = [(s, c) for s in sizes for c in colors]
>>> tshirts
[('S', 'block'), ('S', 'white'), ('M', 'block'), 
('M', 'white'), ('L', 'block'), ('L', 'white')]
```

#### 生成器表达式

虽然也可以用列表推导来初始化元组、数组或其他序列类型，但是生成器表达式是更好的选择。这是因为生成器表达式背后遵循了迭代器协议，可以逐个地产生元素，而不是先建立一个完整的列表，然后把这个列表传递到某个构造函数中。

生成器更加节省内存。
```python
>>> symbols = '$¢£¥€¤'
>>> tuple(ord(s) for s in symbols)
(36, 162, 163, 165, 8364, 164)
```
如果生成器表达式是一个函数调用过程中的唯一参数，那么不需要额外用括号将它包围起来。如果不唯一，那么要包围。

如果用生成器，生成器表达式会在每次for循环运行时才生成一个组合，不会再用大量内存空间：
```python{cmd}
sizes = ['S', 'M', 'L']
colors = ['block', 'white']
for tshirt in (f'{s} {c}' for s in sizes 
                          for c in colors):
    print(tshirt)
```

### 元组不仅仅是不可变的列表

有些 Python 入门教程将元组称为是 “不可变列表”，然而这并没有完全概括元组的特点。

除了用作不可变的列表之外，它还可以用于没有字段名的记录。

#### 元组-记录

元组其实是对数据的记录：元组中的每个元素都存放了记录中的一个字段的数据，外加这个字段的位置，正是这个位置信息给数据赋予了意义。

如果只把元组理解为不可变的列表，那其他信息——它所含有的元素的总数和它们的位置似乎就变得可有可无了。但是如果把元组动作一些字段的集合，那么数量和位置信息就变得非常重要。

元组拆包：
```python {cmd}
a = ['black', 'white', 'yellow', 'red', 'green', 'blue']
# 获取第三、四个元素，忽略其他的
_,_,c,d,*rest = a
print(c,d)
# 获取最后两个元素
*head,c,d = a
print(c,d)
```

元组的拆包是可以嵌套的：
```python {cmd}
vehicles = [
    (87, 'BWM', (35.3, 143.6)),
    (98, 'Ford', (33.7, -120.7)),
    (102, 'Porsche', (23.9, 117.2))
]
for speed, brand, (lantitude,longitude) in vehicles:
    if longitude > 0:
        print(f'{brand} : ({lantitude}, '
        f'{ longitude}), {speed}km/s')
```

#### 具名元组

`collections.namedtuple` 是一个工厂函数。

用 namedtuple 构建的类的实例所消耗的内存跟元组是一样的，因为字段名被存在对应的类里面。这个实例跟普通的对象实例比起来也要小一些，因为 Python 不会用 `__dict__` 来存放这些实例的属性。

```python {cmd id="20180415214553"}
from collections import namedtuple
City = namedtuple('City', 'name population coordinates')
beijin = City('Beijin', 2170, (116, 40))
```
```python {cmd continue}
print(beijin.population)
print(beijin.coordinates)
print(beijin[0])
```
除了从普通元组继承的属性外，还有 `_fields`类属性、`_make(iterable)`类方法和实例方法`_asdict()`:
```python {cmd continue="20180415214553"}
print(City._fields)
```

#### tuple没有的方法

进行修改的方法：+=、-=、*=、append、`__delitem__`、clear、extend、insert、remove、pop、reverse

没意义的方法：copy（因为元组不能修改，没必要深度拷贝）

### 切片

在 Python 中，像list、tuple和str这类序列类型都支持切片操作，但是实际上切片化操作比人们所想象的要强大得多。

#### 为什么切片和区间会忽略最后一个元素

在切片和区间操作中不包含区间范围的最后一个元素是Python的风格，这个习惯符合 Python、C和其他语言中以0作为其实下标的传统，好处：
- range(3) 和 my_list[:3] 都返回3个元素
- 方便计算长度，my_list[start:stop] 共stop-start个元素
- 便于分割：my_list[:x] 和 my_list[x:]不会重合

Python 解释器对切片操作的理解：
`s[a:b:c]` 的形式对 s 在 a 和 b 之间以 c 为间隔取值。c 的值可以为负，表示方向取值。

#### 多维切片和省略

[] 运算符还可以使用以逗号分开的多个索引或者是切片，外部库 NumPy 就是用到了这个特性，二维的 numpy.ndarray 就可以用 a[i,j] 这种形式来获取，抑或是 a[m:n, k:l] 的方式来得到二维切片。

**Python 内置的序列类型都是一维的，因此它们只支持单一的索引，成对出现的索引是没有用的。**

省略(ellipsis) 的正确书写方法是三个英语句号，在 NumPy 中，x[i,...] 就是 x[i,:,:,:]的缩写。

一个错误的嵌套列表使用方法：
```python
>>> weird_board = [['-']*3]*3
>>> weird_board
[['-', '-', '-'], ['-', '-', '-'], ['-', '-', '-']]
>>> weird_board[0][1] = 'o'
>>> weird_board
[['-', 'o', '-'], ['-', 'o', '-'], ['-', 'o', '-']]
```
正确的应该是：
```python
>>> normal_board = [['-']*3 for i in range(3)]
>>> normal_board
[['-', '-', '-'], ['-', '-', '-'], ['-', '-', '-']]
>>> normal_board[0][1] = 'o'
>>> normal_board
[['-', 'o', '-'], ['-', '-', '-'], ['-', '-', '-']]
```

### 序列的增量赋值

+= 背后的特殊方法是`__iadd__` ，但是如果一个类没有实现这个方法的话，Python就会退一步调用 `__add__`。

总的来说，可变序列一般都实现了 `__iadd__` 方法，因此 += 是就地加法，而不可变序列根本就不支持这个操作，所以会关联到新的变量上。

```python
>>> l = [1,2,3]
# id : return the identity of an object
>>> id(l)
4368634376
>>> l *= 2
>>> id(l)
4368634376
>>> t = (1,2,3)
>>> id(t)
4368618984
>>> t *= 2
>>> id(t)
4364255592
```
从上面的例子可以看出，可变序列的 id 在增量赋值时不会改变，不可变序列在增量赋值是会改变 id。

对不可变序列进行重复拼接的时候，效率很很低，因为每次都有一个新对象，而解释器需要把原来对象中的元素先复制到新的对象中，然后追加新的元素。【str 是个例外，效率也很快，毕竟对字符串的 += 操作也非常普遍】

一个 += 的谜题：
```python
>>> t = (1,2,[3,4])
>>> t[2] += [5,6]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'tuple' object does not support item assignment
>>> t
(1, 2, [3, 4, 5, 6])
```
在抛出异常的同时，居然实现了功能。

查看 `s[a] += b` 操作的字节码：
```python
>>> dis.dis('s[a]+=b')
  1           0 LOAD_NAME                0 (s)
              2 LOAD_NAME                1 (a)
              4 DUP_TOP_TWO
              6 BINARY_SUBSCR
              8 LOAD_NAME                2 (b)
             10 INPLACE_ADD
             12 ROT_THREE
             14 STORE_SUBSCR
             16 LOAD_CONST               0 (None)
             18 RETURN_VALUE

```
可以看出：在第 10 处，进行了增量赋值操作，在第 14 处对 s 进行赋值。而如果s第不可变序列，就会出错。

总结：
- 不要把可变对象放在元组中
- 增量赋值不是一个原子操作，它虽然抛出了异常，也实现了功能
- 查看 Python 的字节码并不难，对了解代码背后的运行机制非常有帮助

### list.sort 方法和内置函数 sorted

`list.sort` 方法会就地排序列表，也就是说不会把原列表复制一份。这也是这个方法的返回值是 None 的原因。

与 `list.sort` 相反的是内置函数 sorted，它会新建一个列表作为返回值。这个方法可以接受任何形式的可迭代对象作为参数，甚至包括不可变序列或生成器。而不管 sorted 接受的是怎样的参数，它最后都会返回一个列表。

不管是 list.sort 还是 sorted ，它都接受两个参数 *key* 和 *reverse*。

### 用 bisect 来管理已排序的序列

bisect 模块中包含两个主要函数，bisect 和 insort，两个函数都利用二分查找算法在有序序列中查找或插入元素。

- `bisect(haystack, needle)` : 在 haystack 中搜索 needle 的位置，该位置满足的条件是：把 needle 插入该位置后，haystack 仍然保持升序。也就是说：这个函数返回的位置前面的值，都小于或等于 needle 的值。其中 haystack 必须是有序序列。
```python
>>> import bisect
>>> l = [2,5,7]
>>> bisect.bisect(l,3)
1
>>> l.insert(1, 3)
>>> l
[2, 3, 5, 7]
>>> bisect.insort(l,4)
>>> l
[2, 3, 4, 5, 7]
```

### 当列表不是首选时

虽然列表既灵活又简单，但面对各种需求时，我们可能会有更好的选择，数组 `array` 的效率要高得多，因为数组在背后存的并不是 float 对象，而是数字的机器翻译，即字节表述。这一点就跟 C 语言中的数组一样。

再比如，若要频繁对序列进行先进先出的操作，`deque` 的速度回更快。

而 set 的包含操作（检查一个元素是否出现在一个集合中）效率很高。

#### 数组

如果我们需要一个只包含数字的列表，那么 array.array 比 list 更加高效，包括 `.pop` `.insert` `.extend`。另外，数组还提供了从文件读取和存入的更快的方法：`.fromfile` 和 `.tofile`。

Python 数组跟 C 语言数组一样精简，创建数组需要一个类型码，用于表示在底层的 C 语言中应该存放怎样的数据类型，比如 b 类型表示有符号字符（signed char），因此 `array('b')` 创建出的数组就只能存放一个字节大小的整数，范围从 -128~127，这样在序列很大的时候，我们能节省很多空间。

另外，一个快速序列化数字类型的方法是使用：`pickle` 模块。pickle.dump 处理发电数组的速度几乎跟 array.tofile 一样快。不过 `pickle` 可以处理几乎所有的内置函数字类型，包括数字、嵌套集合，甚至用户自定义的类。前提是这些类没有什么特别复杂的实现。

从 Python 3.4 开始，数组类型不在支持诸如 `list.sort()` 这种就地排序方法。要给数组排序的话，得用 sorted 函数新建一个数组。
```python
a = array.array(a.typecode, sorted(a))
```
要在不打乱次序的情况下为数组添加新的元素，可以用`bisect.insort`。

#### 内存视图

`memoryview` 是一个内置类，它能让用户在不复制内容的情况下操作同一个数组的不同切片。`memoryview` 的概念受到了 NumPy 的启发。

> memoryview 其实是泛化和去数学化的NumPy数组。它让你在不需要复制内容的前提下，在数据结构之间共享内存。其中，数据结构可以是任何形式，比如 PIL 图片、SQLite数据库 和 NumPy 的数组，等等。这个功能在处理大型数据集合的时候非常重要。

`memoryview.cast` 和 C语言中的类型转换非常类似。memoryview.cast 会把同一块内存里的内容打包成一个全新的 `memoryview` 对象。
```python
>>> numbers = array('h',[-2,-1,0,1,2])
>>> memv = memoryview(numbers)
>>> memv[0]
-2
>>> len(memv)
5
>>> memv_oct = memv.cast('B')
>>> list(memv_oct)
[254, 255, 255, 255, 0, 0, 1, 0, 2, 0]
>>> memv_oct[5] = 4
>>> numbers
array('h', [-2, -1, 1024, 1, 2])
```

如果利用数组来做高级的数字处理是你的日常工作，那么 NumPy 和 SciPy 应该是你的常用武器。

**NumPy 和 SciPy**

凭借着 NumPy 和 SciPy 提供的高阶数组和矩阵操作，Python 成为了科学计算应用的主流语言。

**双向列表**
`list` 利用 append 和 pop 可以模拟先进后出的堆栈操作。也可以通过 append 和 pop(0) 来模拟队列操作，不过删除列表第一个元素（或者是在第一个元素之前添加一个元素）之类的操作都是很耗时的，因为这些操作会牵扯到移动列表中的所有元素。

`collections.deque` 类是一个线程安全、可以快速从两端添加或者删除元素的数据类型。而如果想要有一种数据类型存放 “最近使用到的几个元素”，deque也是一个很好的选择。**新建一个双向队列的时候可以指定队列的大小，如果队列满员，还可以从反向端删除过期的元素，然后在尾端添加新的元素。**

`deque([iterable[, maxlen]]) --> deque object`，maxlen 是一个可选参数，代表这个队列可以容纳的元素数量，一旦设定，这个属性就不能修改了。

append 和 popleft 都是原子操作，也就是说 deque 可以在多线程中安全地当先进先出的栈使用，而不需要担心资源锁的问题。

除了 deque 之外，还有一些其他的Python标准库也有对队列的实现。

queue：提供了同步(线程安全)类Queue、LifoQueue和PriorityQueue，不同的线程可以利用这些数据类型来交换信息。这3个类的构造方法都有一个可选参数 maxsize，它接受正整数作为输入值，用来限定队列的大小。但是在满员的时候，这些类不会扔掉就的元素来腾出位置，而是会被锁住，直到另外的线程移除了某个元素而腾出位置。这一特性让这些类很适合用来控制活跃线程的数量。

asyncio：Python3.4 新提供的包，里面有 Queue、LifoQueue、PriorityQueue 和 JoinableQueue，这些类受到 queue 和 multiprocessing 模块的影响，但是为异步编程里的任务管理提供了专门的便利。

注意：list 中最好存放同类型的数据，至少是存放能够相互比较的数据，否则 sort 和 sorted 都不能使用了。而 tuple 并经常用来存放不同类型的元素，这也符合它的本质，元组就是用作存放彼此之间没有关系的数据的记录，也并不需要进行比较。

**key 参数很妙** ：list.sort/sorted/max/min函数的key参数使一个很棒的设计，其他语言里的排序函数需要用户提供一个接受两个参数的比较函数作为参数，比如 Python2 中的 cmp(a,b) 。用 key 参数能把事情变得简单且高效，简单是因为只需要提供一个单参数函数来提取或计算一个值作为比较大小的标准即可，而 Python2 的这种设计需要用户写一个返回值是 -1、0或1的双参数函数。说它高效，是因为在每个元素上，key 函数只会被调用一次，而双参数比较函数则在每次两两比较的时候都会被调用。诚然，在排序的时候，Python总会比较两个键 key，但是那一阶段的计算会发生在C语言那一层，这样会比调用用户自定义的Python比较函数更快。

另外，key参数也能让你对一个混有数字字符和数值的列表进行排序：
```python
>>> a = ['12',1,9,'8',3,'15',6]
>>> sorted(a, key=int)
[1, 3, 6, '8', 9, '12', '15']
```

sorted 和 list.sort 背后的排序算法是 Timsort，它是一种自适应算法，会根据原始数据的顺序特点交替使用插入排序和归并排序，以达到最佳效率。这样的算法被证明是很有效的，因为来自真实世界中的数据通常是有一定的顺序特点的。

## 字典和集合

> 字典这个数据结构活跃在所有 Python 程序的背后，即使你的编码中没有直接用到它。——A.M. Kuchling

dict 类型不但在各种程序中广泛使用，**它也是 Python语言的基石**。模型的命名空间、实例属性和函数的关键字参数中都可以看到字典的身影，跟它有关的内置函数都在 `__builtins__.__dict__`模块中。

正是因为字典至关重要，Python 对它的实现做了高度优化，而散列表则是字典类型性能出众的根本原因。

要想进一步理解集合和字典，首先要理解散列表的原理。

标准库中的所有映射类型都是利用 dict 来实现的，因此它们有一个共同的限制，即只有可散列的数据类型才能用作这些映射里的键。

什么是可散列的？
> 如果一个对象是可散列的，那么这个对象的生命周期中，它的散列值是不变的，而且这个对象需要实现 `__hash__()` 方法。另外，可散列对象还要有 `__qe__()` 方法，这样才能跟其他键作比较。如果两个散列对象是相等的，那么它们的散列值一定是一样的。

原子不可变数据类型(str, bytes, 数值类型)都是可散列的，frozenset 也是可散列的。元组的话，只有当一个元组包含的所有元素都是可散列类型的情况下，它才是可散列的：
```python
>>> tt = (1,2,(30,40))
>>> hash(tt)
8027212646858338501
>>> t1=(1,2,[30,40])
>>> hash(t1)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: unhashable type: 'list'
>>> t1 = (1,2,frozenset([30,40]))
>>> hash(t1)
-4118419923444501110
```
字典定义了很多的构造方法：
```python
>>> a = {'one':1, 'two':2, 'three':3}
>>> b = dict(one=1, two=2, three=3)
>>> c = dict(zip(['one','two','three'],[1,2,3]))
>>> d = dict([('one',1),('two',2),('three',3)])
>>> e = dict({'one':1, 'two':2, 'three':3})
```
除了这些字面句法和灵活的构造方法外，字典推导(dict comprehension)也可以用来创建新的dict。

### 字典推导

自 Python2.7 以来，列表推导和生成器表达式的概念就移植到了字典上，从而有了字典推导。
```python
>>> pairs = [('one',1),('two',2),('three',3),
...     ('four',4),('five',5),('six',6)]
>>> d = {k:v for k,v in pairs}
>>> d
{'one': 1, 'two': 2, 'three': 3, 'four': 4, 'five': 5, 'six': 6}
>>> d = {k:v for k,v in pairs if v%2==0}
>>> d
{'two': 2, 'four': 4, 'six': 6}
```

### 常见的映射方法

表3-1：`dict`、`collections.defaultdict` 和 `collections.OrderedDict`这三种映射类型的方法列表：
||dict|defaultdict|OrderedDict||
|---|---|---|---|---|
|`d.clear()`|*|*|*|移除所有元素|
|`d.__contains__(k)`|*|*|*|检查k是否在d中|
|`d.copy()`|*|*|*|浅复制|
|`d.__copy__()`||*||用于支持 copy.copy|
|`d.default_factory`||*||默认值|
|`d.__delitem(k)`|*|*|*|del d[k],移除键为k的元素|
|`d.fromkeys(it,[initial])`|*|*|*||
|`d.get(k,[default])`|*|*|*||
|`d.__getitem__(k)`|*|*|*|获取d[k]|
|`d.items()`|*|*|*|返回d中所有键值对|
|`d.__iter__()`|*|*|*|获取键的迭代器|
|`d.keys()`|*|*|*|获取所有的键|
|`d.__len__()`|*|*|*|len(d)|
|`d.__missing__(k)`||*||d[k]找不到k时被调用|
|`d.move_to_end(k,[last])`|||*|将k的元素移到最靠后或最靠前位置|
|`d.pop(k,[default])`|*|*|*||
|`d.popitem()`|*|*|*|随机弹出一个键值对|
|`d.__reversed__()`|||*|对键进行倒叙排列|
|`d.setdefault(k,[default])`|*|*|*||
|`d.__setitem__(k,v)`|*|*|*|d[k]=v|
|`d.update(m,[**kargs])`|*|*|*|用m中内容更新d对应条目|
|`d.values()`|*|*|*|返回字典中的所有值|

**鸭子类型(duck typing)**：一种动态类型的风格，在这种风格中，一个对象有效的语义不是由继承自特定的类或特定的接口决定，而是由 *当前方法和属性的集合* 决定。

鸭子测试可以表述为：当看到一只鸟走起来像鸭子、游泳像鸭子、叫起来也像鸭子，那这只鸟就可以被称为鸭子。

