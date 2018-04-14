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
