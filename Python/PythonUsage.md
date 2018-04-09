---
export_on_save:
 html: true
export LC_ALL:
 en_US.UTF-8
---

@import "/blog_head.md"

# Python 使用

## 数据结构与算法

### 压缩序列的赋值

压缩序列可以是如何可迭代对象。

```python {cmd}
a,b,c = [1,2,3]
print("1:", a,b,c)
a,b,c = "xyz"
print("2:", a,b,c)
a,*b,c = "1234567"
print("3:", a,b,c)
# 带*的解压出来都是list类型
a,*b,c="13"
print("4:", a,b,c)

def f1(a,*b):
    print("5:", a,b)
f1(1,3,4,5)

def f2(a,b):
    print("6:", a,b)
f2(*(1,2))
```

### collections.Counter

该类用于计算序列中出现次数最多的元素。
```python {cmd}
from collections import Counter
words = [
    'look', 'into', 'my', 'eyes', 'look', 'into', 'my', 'eyes',
    'the', 'eyes', 'the', 'eyes', 'the', 'eyes', 'not', 'around', 'the',
    'eyes', "don't", 'look', 'around', 'the', 'eyes', 'look', 'into',
    'my', 'eyes', "you're", 'under'
]
word_counts = Counter(words)
print(word_counts.most_common(3))
print(word_counts['into'])
word_counts.update(['into','into','eyes'])
print(word_counts.most_common(3))
```
`Counter`对象可以接受任意的由可哈希(hashable)元素构成的序列对象。

`Counter` 对象可以继续加减运算：
```python {cmd}
from collections import Counter
words1 = ['in', 'the', 'a', 'an', 'in', 'the', 'the']
words2 = ['the', 'a', 'with', 'in']
a = Counter(words1)
b = Counter(words2)
print(a)
print(b)
print(a+b)
print(a-b)
```
### 通过某个关键字排序字典列表
```python {cmd}
rows = [
    {'fname': 'Brian', 'lname': 'Jones', 'uid': 1003},
    {'fname': 'David', 'lname': 'Beazley', 'uid': 1005},
    {'fname': 'John', 'lname': 'Cleese', 'uid': 1001},
    {'fname': 'Big', 'lname': 'Jones', 'uid': 1004}
]
print("---sort by fname---")
for i in sorted(rows, key=lambda r:r['fname']):
    print(i)
print("\n---sort by uid (reverse)---")
for i in sorted(rows, key=lambda r:r['uid'], reverse=True):
    print(i)

print("\n---sort by multi-keywords(1)---")
for i in sorted(rows, key=lambda r:(r['lname'],r['fname'])):
    print(i)

# 使用 itemgetter 速率会稍稍快点
print("\n---sort by multi-keywords(2)---")
from operator import itemgetter
for i in sorted(rows, key=itemgetter('lname','fname')):
    print(i)

# max / min
print("\n---max / min---")
print(max(rows,key=lambda r:r['uid']))
print(min(rows, key=lambda r:r['fname']))
```

### 排序比支持原生比较的对象
```python {cmd}
class User:
    def __init__(self, user_id):
        self.user_id = user_id
    def __repr__(self):
        return f"User ({self.user_id})"
users = [User(23), User(3), User(99)]
print(sorted(users, key=lambda u:u.user_id))
from operator import attrgetter
print(sorted(users, key=attrgetter('user_id')))
```

### 通过某个字段将记录分组

```python {cmd}
from itertools import groupby
from operator import itemgetter
rows = [
    {'address': '5412 N CLARK', 'date': '07/01/2012'},
    {'address': '5148 N CLARK', 'date': '07/04/2012'},
    {'address': '5800 E 58TH', 'date': '07/02/2012'},
    {'address': '2122 N CLARK', 'date': '07/03/2012'},
    {'address': '5645 N RAVENSWOOD', 'date': '07/02/2012'},
    {'address': '1060 W ADDISON', 'date': '07/02/2012'},
    {'address': '4801 N BROADWAY', 'date': '07/01/2012'},
    {'address': '1039 W GRANVILLE', 'date': '07/04/2012'},
]
# groupby 之前需要先排序
rows.sort(key=itemgetter('date'))
for date, items in groupby(rows, key=itemgetter('date')):
    print(date, ":")
    for item in items:
        print("   ", item)
```

**注意：一个非常重要的准备步骤是在`groupby`之前进行数据排序，因为`groupby`仅仅检查连续的元素。**

### 过滤序列元素

```python {cmd}
mylist = [1,4,-5,10,-7,2,3,1]
# 求出大于0的元素
# 列表推导：缺点--输出非常大时需要产生一个大结果集，占内存
print([i for i in mylist if i>0])
# 生成器
g = (i for i in mylist if i>0)
for i in g:
    print(i, end=" ")
print()
# 将负数变为正数
print([i if i>0 else -i for i in mylist])

# 对于复杂情况 用filter，返回迭代器
values = ['1','2','-3','-','4','N/A','5']
def is_int(val):
    try:
        int(val)
        return True
    except ValueError:
        return False
print(list(filter(is_int, values)))

# 输出指定位置的数据 compress返回迭代器
from itertools import compress
output_e = [True]*len(mylist)
output_e[0]=output_e[3]=output_e[4]=False
print(list(compress(mylist, output_e)))
```


### 字典的最大值/最小值/排序
```python {cmd}
prices = {
    'ACME':45.23,
    'AAPL':612.78,
    'IBM':205.55,
    'HPQ':37.20,
    'FB':10.75
}
# 求键值最小/最大的键名
print(f"1 : {min(prices)}")
print(f"2 : {max(prices)}")
print(f"3 : {sorted(prices)}")
# 求值最小/最大的键名
print(f"4-1 : {min(zip(prices.values(), prices.keys()))[-1]}")
# lambda 后面跟的s是通过 in 可取的迭代器中的值
print(f"4-2 : {min(prices, key=lambda s:prices[s])}")
print(f"5-1 : {max(zip(prices.values(), prices.keys()))[-1]}")
print(f"5-2 : {max(prices, key=lambda s:prices[s])}")
print(f"6 : {[i[-1] for i in sorted(zip(prices.values(), prices.keys()))]}")

# 当值存在重复时 max的结果为key较大的
prices = {'AAA':20, 'ZZZ':20, 'BBB':20}
print(f"7 : {max(zip(prices.values(), prices.keys()))[-1]}")

# 寻找两个字典系统的键
a = {'x':1, 'y':2, 'z':3}
b = {'w':10, 'x':11, 'y':2}
# 集合求交操作
print(f"8-1 : {[i for i in a if i in b]}")
print(f"8-2 : {a.keys() & b.keys()}")
# 集合求差操作
print(f"9-1 : {[i for i in a if i not in b]}")
print(f"9-2 : {a.keys() - b.keys()}")
# 集合求异操作
print(f"10 : {a.keys()^b.keys()}")
# 集合并操作
print(f"11 : {a.keys()|b.keys()}")

# 寻找两字典键值对相等的item
print(f"12-1 : {[(i,a[i]) for i in a if i in b and a[i]==b[i] ]}")
print(f"12-2 : {a.items() & b.items()}")

# 输出指定键名的item
print(f"13-1 : { {i:a[i] for i in a.keys()-{'x'}} }")
print(f"13-2 : { {i:a[i] for i in a if i not in ['x']} }")
```
注：`zip()`函数创建的是一个只能访问一次的迭代器！

字典的`keys()`方法返回一个展现键集合的键视图对象，其支持集合操作：并(`|`)、交(`&`)、差(`-`)、求异(`^`)。这4中操作对 `set` 类型适用。如果想对集合的键执行一些普通的集合操作，可以直接使用键视图对象，而不用现将它们转换成一个`set`。

字典的`items()`也支持集合操作。但`values()`不支持集合操作。

### 从字典中提取子集

```python {cmd}
prices = {
    'ACME': 45.23,
    'AAPL': 612.78,
    'IBM': 205.55,
    'HPQ': 37.20,
    'FB': 10.75
}
# 获得值大于200的items
# 使用字典推导
p1 = {k:v for k,v in prices.items() if v>200}
print(p1)
tech_names = ["FB", "IBM", "ACME"]
p2 = {k:v for k,v in prices.items() if k in tech_names}
print(p2)
```

### 字典合并
```python {cmd}
from collections import ChainMap
a = {'x':1, 'z':2}
b = {'y':2, 'z':4}
c = ChainMap(a, b)
print("ChinaMap result:")
print(c['x'])
print(c['y'])
print(c['z'])
print(c)
print(list(c.keys()))
print(list(c.values()))
# ChainMap 只是存储了一个视图而已
print("\nChainMap just store a view:")
a['x'] = 10
print(c)
print(list(c.values()))
c['z'] = 20
print(a)

print("\ndict.update demo:")
# 也可使用update进行合并
d = dict(b)
d.update(a)
print(d)
# dict.update进行了拷贝而非视图
d['x'] = 30
print(a)
```

### 映射名称到序列元素 namedtuple

```python {cmd}
from collections import namedtuple
Subscriber = namedtuple('Subscriber', ['addr', 'joined'])
sub = Subscriber('jonesy@example.com', '2012-10-19')
print(sub)
print(sub.addr)
print(sub.joined)

# 支持所有的普通元组操作
print(len(sub))
print(sub[-1])
addr, joined = sub
print(addr, joined)
```

**命名元组的一个主要用途是将你的代码从下表操作中解脱出来。**

```python
# 普通元组
def compute_cost(records):
    total = 0.0
    for rec in records:
        total += rec[1]*rec[2]
    return total
# namedtuple
from collections import namedtuple
Stock = namedtuple('Stock', ['name', 'shares', 'price'])
def compute_cost(records):
    total = 0.0
    for rec in records:
        s = Stock(*rec)
        total += s.shares * s.price
    return total
```

**命名元组的另一个用途就是作为字典的替代，因为字典存储需要更多的内存空间。如果你需要构建一个非常大的包含字典的数据结构，那么使用命名元组会更加高效。但是：一个命名元组是不可更改的！**

如果真的需要改变属性的值，那么可以使用命名元组实例的 `_replace()`方法，它会创建一个全新的命名元组并将对应的字段用新的值取代：
```python {cmd}
from collections import namedtuple
Stock = namedtuple('Stock', ['name', 'shares', 'price'])
s = Stock('ACME', 100, 73.5)
print(s)
s = s._replace(shares=80)
print(s)
```

### 转换并计算数据

问题：你需要在数据序列上执行聚集函数，比如 `sum()`、`min()`、`max()`，但首先需要转换或过滤数据：
```python {cmd}
nums = [1,2,3,4,5]
# 求平方和
s = sum(x**2 for x in nums)
print(s)
```

注意：在`for`中的语句执行1遍，其他的执行循环的次数：
```python {cmd}
def f(x):
    print("call f")
    return x**2
a = [1,2,3,4,5,6,7]
print("---test 1---")
sum(x for x in range(f(3)))

print("\n---test 2---")
sum(f(x) for x in range(3))

print("\n---test 3---")
sum(x for x in a if x < f(3))
```
上面演示的是生成器表达式作为一个单独参数传递给函数时候的巧妙语法（并不需要多加一个括号），例如：
```python {cmd}
nums = [1,2,3,4,5]
print(sum(n for n in nums))
print(sum((n for n in nums)))
```

### 删除序列系统元素并保持顺序

```python {cmd}
a = [1,5,2,1,9,1,5,10]
# 会打乱顺序
print(1, ":", list(set(a)))
def dedupe(items):
    seen = set()
    for item in items:
        if item not in seen:
            yield item
        seen.add(item)
print(2, ":", list(dedupe(a)))
```
上面的方法只在序列中元素为hashable的时候才管用。消除不可哈希（如`dict`类型）的序列中重复元素：
```python {cmd}
a = [{'x':1, 'y':2}, {'x':1, 'y':3}, 
{'x':1, 'y':2}, {'x':2, 'y':4}]
def dedupe(items, key=None):
    seen = set()
    for item in items:
        val = item if key==None else key(item)
        if val not in seen:
            yield item
        seen.add(val)
print(list(dedupe(a, lambda s:(s['x'],s['y']))))
print(list(dedupe(a, lambda s:s['x'])))
```

### 命名切片

将切片从硬切片中独立出来：

```python {cmd}
record = '....................100 .......513.25 ..........'
# 硬切片方式
print(int(record[20:23])*float(record[31:37]))
# 命名切片方式
SHARES = slice(20, 23)
PRICE = slice(31,37)
print(int(record[SHARES])*float(record[PRICE]))
```

命名切片的优点：**一般来说，代码中如果出现大量的硬编码下标值，会使得可读性和可维护性大大降低，命名切片可以清晰地表达代码的逻辑。**

`slice`的使用：
```python {cmd}
items = [0,1,2,3,4,5,6]
a = slice(2,5,2)
print(a)
print(a.start, a.stop, a.step)
items[a] = [10,20]
print(items)
```

### defaultdict 模块

用于构建一个键对应多个值得字典（也称为 **multidict**）；

#### 使用 dict+容器
```python
d = {
    'a':[1,2,3],
    'b':[4,5]
}
e = {
    'a':{1,2,3},
    'b':{4,5}
}
```

#### 使用 collections.defaultdict
```python {cmd}
from collections import defaultdict
d = defaultdict(list)
d['a'].append(1)
d['a'].append(2)
d['b'].append(4)

e = defaultdict(set)
e['a'].add(1)
e['a'].add(2)
e['b'].add(4)
print(d)
print(e)
```

#### 自动创建键
`defaultdict`会自动为将要访问的键（就算当前字典中并不存在这样的键）创建映射实体。如果你并不需要这样的特性，你可以在一个普通的字典上使用`setdefault()`方法替代：
```python {cmd}
d = {}
d.setdefault('a',[]).append(1)
d.setdefault('a',[]).append(2)
d.setdefault('b',[]).append(4)
print(d)
```

使用`defaultdict`:
```python {cmd}
from collections import defaultdict
d = defaultdict(list)
d['a'].append(1)
d['a'].append(2)
d['b'].append(4)
print(d)
```

### heapq 模块

#### 获得集合中最大/最小的N歌元素列表
```python {cmd}
import heapq
nums = [12,33,41,64,75,27,94,67,28]
print(heapq.nlargest(3, nums))
print(heapq.nsmallest(3, nums))
```

两个函数都能接收一个关键字参数：
```python {cmd}
import heapq
portfolio = [
    {'name': 'IBM', 'shares': 100, 'price': 91.1},
    {'name': 'AAPL', 'shares': 50, 'price': 543.22},
    {'name': 'FB', 'shares': 200, 'price': 21.09},
    {'name': 'HPQ', 'shares': 35, 'price': 31.75},
    {'name': 'YHOO', 'shares': 45, 'price': 16.35},
    {'name': 'ACME', 'shares': 75, 'price': 115.65}
]
print(heapq.nlargest(2, portfolio, lambda s:s['price']))
print(heapq.nsmallest(2, portfolio, lambda s:s['price']))
```

#### 堆排序
```python {cmd}
import heapq
nums = [12,33,41,64,75,27,94,67,28]
a = list(nums) # 相当于 nums.copy()
heapq.heapify(a)
print(a)
```

#### 注意

- 当求最大/最小的一个值时，使用`max()/min()`
- 当求的元素个数较少，但不是一个时，使用`heapq.nlargest() / heapq.nsmallest()`
- 当求的元素个数较多，接近于集合个数时，使用`sorted(a)[:N] / sorted(a, reverse=True)[:N]`

#### 按优先级排序的队列

```python {cmd}
import heapq
class PriorityQueue:
    def __init__(self):
        self._queue = []
        self._index = 0
    def push(self, item, priority):
        heapq.heappush(self._queue, (-priority, self._index, item))
        self._index += 1
    def pop(self):
        return heapq.heappop(self._queue)[-1]
class Item:
    def __init__(self, name):
        self.name = name
    def __repr__(self):
        return f"Item({self.name})"
q = PriorityQueue()
q.push(Item('foo'), 1)
q.push(Item('bar'), 5)
q.push(Item('spam'), 4)
q.push(Item('grok'), 1)
print(q.pop())
print(q.pop())
print(q.pop())
print(q.pop())
```

### collections 模块

#### deque

deque (double-ended queue) 双端队列，是一种同时具有队列和堆栈性质的数据结构。

```python {cmd}
from collections import deque
q = deque(maxlen=3)
q.append(1)
q.append(2)
q.append(3)
print(q)
q.append(4)
print(q)
q.appendleft(5)
print(q)
q.pop()
print(q)
q.popleft()
print(q)
```
可以通过`list`实现，但使用`collections.deque`队列方案更加优雅，并且运行更快。

在队列两端插入或删除元素的时间复杂度为$O(1)$，而在列表的开头插入或删除元素的时间复杂度为$O(N)$

### yield 的使用

#### 可迭代对象

像`list`、`tuple`、`dict`、`文件`等可以用`for . in ...`语法进行遍历的类型称为可迭代对象；你把所有的值都存放在内存中，然后逐个取出，当可迭代对象作用内存非常大时，这种方法不可取。
```python {cmd}
a = [x*x for x in range(3)]
print(a)
for i in a:print(i, end=" ")
else: print()
for i in a:print(i, end=" ")
else: print()
```

#### 生成器

生成器是可迭代的，但是你 **只可以读取它一次**，因为它不是将所有数据都存放在内存中，而是实时生成的。

生成器在创建时用小括号：
```python {cmd output="text"}
b = (x*x for x in range(3))
print(b)
for i in b:print(i, end=" ")
else: print()
for i in b:print(i, end=" ")
else: print()
```
在运行的过程中计算出值。

#### yield

`yield` 是一个类似于 `return` 的关键字，只是这个函数返回的是生成器。

```python {cmd}
def create_generator():
    for i in range(3):
        yield i
g = create_generator()
for i in g:print(i, end=" ")
print()
for i in g:print(i, end=" ")
```

### 丢失的三元运算符 ?:

在C、C++、JavaScript中有三元运算符 `?:`：
```node {cmd}
a = 5
b = 9
console.log(a>b?a:b)
```
python 中的代价语法：
```python {cmd}
a = 5
b = 9
print(a if a>b else b)
```

### 丢失的for语句
在C、C++、JavaScript中有`for`语句：
```node {cmd}
for(i=0; i<10; ++i){
    if(i%2==0){
        console.log(i)
    }
}
```

在Python中：
```python {cmd}
[print(i, end=" ") for i in range(10) if i%2==0]
```

### Python的格式化字符串

#### 使用\%操作符

从版本1.0就存在的字符串格式化方式：
```python {cmd}
name = 'Fred'
height = 176.23
print("My name is %s, height is %.1f cm" %(name, height))
```

#### 使用string.format
从版本2.6开始引入：
```python {cmd}
name = 'Fred'
height = 176.23
print("My name is {0}, height is {1:.1f} cm".format(name, height))
print("My name is {sname}, height is {sheight:.1f} cm".format(sheight=height, sname=name))
```

#### 使用 f 字符串
从版本3.6开始引入：
```python {cmd}
name = 'Fred'
height = 176.23
print(f"My name is {name}, height is {height:.1f} cm")
```


## 内部函数

- `dir`：用于显示模块/类的属性

## Python的类

**Python** 中并不存在真正私有的方法。

### Python类中属性/方法命名

#### 以一个下划线开头 _foo

使用这种命名的原因：
- 不希望它被直接import
- 不希望它被直接访问
- 只是测试中的 function

例如，我们写了一个 `test.py`:
@import "test.py" {cmd="python"}

**真的不能`import`?**

```python {cmd}
from test import *
public_func()
_private_func()
```
上面的代码说明不能通过`from module_name import *` 的方式使用`_foo`函数；

```python {cmd}
from test import _private_func
_private_func()
```
但可以通过 `from modele_name import _foo` 的方式使用`_foo`函数；



#### 以一个下划线结尾 foo_

如`foo_`，这种方式主要是为了避免与Python的 **built-in keywords** 和 **built-in functions** 取一样的名字。

1. 如果命名与**built-in functions**相同：
```python {cmd}
print(list("abc"))
list = 12
print(list("abc"))
```
会造成原 **built-in functions** 不能使用，改进为：
```python {cmd}
print(list("abc"))
list_ = 12
print(list("abc"))
```

2. 如果命名与**built-in keywords**相同：
```python {cmd}
is=12
```
将直接抛出一个语法异常。


- 双下划线开头：在类成员中表示名字改编，这是为了使其避免与子类中的名字冲突

#### \_\_foo\_\_

不要使用，除非你是 Python 的核心开发人员。

#### \_\_foo

```python {cmd}
class A:
    def __foo1(self):
        print("called A::__foo1")
    def foo2(self):
        print("called A::foo2")
    def f(self):
        print("\n--- f ---")
        self.__foo1()
        self.foo2()
        print("-"*9, end="\n"*2)

a = A()
print(dir(a)[:3], "...")
# 实现了变名
a._A__foo1()

a.f()

class B(A):
    def __foo1(self):
        print("called B::__foo1")
    def foo2(self):
        print("called B::foo2")
b = B()
b.f()
```

**总结：**
- 如果要引用`__foo`函数，需要通过变换后的名：`_modulename__foo`
- `__foo` 类似的方法/属性不能被子类重写；

### 类的内置接口

#### \_\_repr\_\_

```python {cmd}
class A:
    def __repr__(self):
        return "I am A"
print(A())
```


## 获取全排列

```python {cmd}
import itertools
for i in itertools.permutations("abc"):
    print(i)
```

## 字符串和文本

### 使用多个界定符分割字符串

```python {cmd}
line = "asdf  fjk;  fj,ek, foo"
import re
print(re.split(r'[\s\;\,]+', line))
fields = re.split(r'([;,\s]+)', line)
print(fields)
print(fields[::2])

# 捕获与非捕获
print(re.split(r'(\s|,|;)+',line))
# (?:...)表示非捕获
print(re.split(r'(?:\s|,|;)+',line))
```
使用`re.split()`函数时，需要特别注意的是正则表达式中是否包含一个括号捕获分组。如果使用了分组捕获，那么被匹配的文本也将出现在结果列表中；


### 字符串开头或结尾匹配

```python {cmd}
import re
# 使用 str.startswith str.endswith
filename = "spam.txt"
print(filename.startswith('file:'))
print(filename.endswith('.txt'))
print(filename[-4:]==".txt")
print(re.match('[\s\S]*\.(txt|c)$', filename))
print(filename.endswith(('.txt','c')))

# 检查匹配并归类
filenames = ['Makefile','foo.c','bar.py','spam.c','spam.h']
print([f for f in filenames if f.endswith(('.c','.h'))])
print(any(f.endswith('py') for f in filenames))
```

### 用 shell 通配符匹配字符串

你可以使用Unix Shell 中常用的通配符（比如：*.py，Dat[0-9]*.csv等）去匹配文本字符串。
```python {cmd}
from fnmatch import fnmatch, fnmatchcase
print(fnmatch('foo.txt', '*.txt'))
print(fnmatch('foo.txt', '?oo.txt'))
print(fnmatch('Dat45.csv','Dat[0-9]*'))
# 大小写敏感(mac os)
print(fnmatch('foo.txt', '*.TXT'))
print(fnmatchcase('foo.txt', '*.TXT'))

addresses = [
    '5412 N CLARK ST',
    '1060 W ADDISON ST',
    '1039 W GRANVILLE AVE',
    '2122 N CLARK ST',
    '4802 N BROADWAY',
]
print([addr for addr in addresses if fnmatch(addr, '* ST')])
print([addr for addr in addresses if fnmatch(addr, '54[0-9]*CLARK*')])
```

`fnmatch()`函数的匹配能力介于简单的字符串方法和强大的正则表达式之间。如果在数据处理操作中只需要简单的通配符就能完成的时候，这通常是一个比较合理的方案。

如果你的代码需要做文件名的匹配，最好使用glob模块。

### 字符串匹配和搜索

```python {cmd}
# 简单的搜索
text = 'yeah, but no, but yeah, but no, but yeah'
print(text.find('no'))
# 对于复杂的匹配，使用 re 模块
import re
text1 = '11/27/2012'
text2 = 'Nov 27, 2012'
datepat = re.compile(r'\d+/\d+/\d+')
# 方式1：re.match
print('yes' if re.match(datepat, text1) else 'no')
# 方式2：pat.match
print('yes' if datepat.match(text2) else 'no')

text = 'Today is 11/27/2012. PyCon starts 3/13/2013.'
# re.match 总是从字符串开始去匹配
print('yes' if datepat.match(text) else 'no')
# 任意位置查找
print(datepat.findall(text))

# 在定义正则式时，通常会利用括号去捕获分组
datepat = re.compile(r'(\d+)/(\d+)/(\d+)')
m = datepat.match('11/27/2012')
print(m.group(0), m.group(1), m.group(2), m.group(3))
print(m.groups())

print(datepat.findall(text))
# 时间格式转换
for month, day, year in datepat.findall(text):
    print(f'{year}-{month}-{day}')
# findall 以列表形式返回，finditer 以生成器方式返回
for m in datepat.finditer(text):
    month, day, year = m.groups()
    print(f'{year}-{month}-{day}')

# match 只检查字符串的开头部分
m = datepat.match("11/27/2012ddasdf")
print('yes' if m else 'no')
```
注意：如果你打算做大量的匹配和搜索操作的话，最好先编译正则表达式，然后再重复使用它。模块级别的函数会将最近编译过的模式缓存起来，不会有太多的性能消耗，但如果使用预编译模式的话，你将会减少查找和一些额外的处理损耗。

### 字符串搜索和替换

```python {cmd}
# 简单的字符串替换
text = 'yeah, but no, but yeah, but no'
print(text.replace('yeah', 'yep'))
# 复杂的字符串替换
import re
text = 'Today is 11/27/2012. PyCon starts 3/13/2013.'
datepat = re.compile(r'(\d+)/(\d+)/(\d+)')
print(datepat.sub(r'\3-\1-\2', text))

# 可读性更强的一种方式
# (?P<name>\d+) 表示捕获为name变量
# \g<name> 使用name
datepat = re.compile(r'(?P<month>\d+)/(?P<day>\d+)/(?P<year>\d+)')
print(datepat.sub(r'\g<year>-\g<month>-\g<day>', text))
```

对于更加复杂的替换，可以传递一个替换回调函数来替代：
```python {cmd}
import re
from calendar import month_abbr
text = 'Today is 11/27/2012. PyCon starts 3/13/2013.'
datepat = re.compile(r'(?P<m>\d+)/(?P<d>\d+)/(?P<y>\d+)')
def change_date(m):
    mon_name = month_abbr[int(m.group('m'))]
    return f"{m.group('d')} {mon_name} {m.group('y')}"
print(datepat.sub(change_date, text))
# 第一个返回值为替换结果，第二个返回值为替换次数
print(datepat.subn(change_date, text))
```

### 字符串忽略大小写的搜索替换

```python {cmd}
import re
text = 'UPPER PYTHON, lower python, Mixed Python'
print(re.findall(r'python', text, flags=re.IGNORECASE))
print(re.sub(r'python', 'snake', text, flags=re.IGNORECASE))

def matchCase(word):
    def replace(m):
        text = m.group()
        if text.isupper():
            return word.upper()
        elif text.islower():
            return word.lower()
        elif text[0].isupper():
            return word.capitalize()
        else:
            return word
    return replace
print(re.sub('python',matchCase('snake'),
    text ,flags=re.IGNORECASE))
```
注：上面的后面的例子中，`matchCase('snake')`返回了一个回调函数。

### 最短匹配模式

你正在使用正则表达式匹配某个文本模式，但是它找到的可能是最长匹配。修改变为最短匹配：
```python {cmd}
import re
text1 = 'Computer says "no."'
text2 = 'Computer says "no." Phone says "yes."'
str_pat = re.compile(r'\"(.*)\"')
print(str_pat.findall(text1))
# 最长匹配的例子(贪婪模式)
print(str_pat.findall(text2))
# 使用最短匹配 在匹配模式后加上 ? (懒惰模式)
print(re.findall(r'\"(.*?)\"', text2))
```

### 多行匹配模式
```python {cmd}
import re
comment = re.compile(r'/\*(.*?)\*/')
text1 = '/* This is one-line of comment */'
text2 = """
/*This is multi-line
 comment*/
"""
print(comment.findall(text1))
print(comment.findall(text2))
# '?'在后面表示最短匹配，'?:'在捕获组的前面表示不进行捕获
comment = re.compile(r'/\*([\s\S]*?)\*/')
print(comment.findall(text2))
comment = re.compile(r"/\*((?:.|\n)*)\*/")
print(comment.findall(text2))
# 使用 re.DOTALL 让点匹配所有字符
print(re.findall(r'/\*(.*)\*/', text2, flags=re.DOTALL))
```

### 删除字符串中不需要的字符
```python {cmd}
import re
# 使用 str.strip、str.lstrip、str.rstrip
# 删除开头/结尾的指定字符串 不会删除中间的字符
s = '   hello   world  '
print(f"'{s.strip()}'")
print(f"'{s.lstrip()}'")
print(f"'{s.rstrip()}'")
# strip 的参数中字符是或的关系
print(f"'{s.strip(' hd')}'")
# 删除中间的空格
print(f"'{s.strip().replace(' ','')}'")
# 删除中间多余的空格

print("'{0}'".format(re.sub(r'\s+', ' ', s.strip())))
```

### 审查清理文本数据字符串
```python {cmd}
# 改写指定字母
text = 'life is short, please use python'
print(text.translate({
    ord('s'):'S',
    ord('p'):'pop'
}))
```

### 字符串对齐
```python {cmd}
hw = "hello,world"
print(f"'{hw.ljust(20)}'")
print(f"\'{format(hw, '<20')}\'")
print("'{:<20}'".format(hw))

print(f"'{hw.rjust(20)}'")
print(f"\'{format(hw, '>20')}\'")
print("'{:>20}'".format(hw))

print(f"'{hw.center(20)}'")
print(f"\'{format(hw, '^20')}\'")
print("'{:^20}'".format(hw))

print(f"'{hw.center(20,'*')}'")
print(f"\'{format(hw, '*^20s')}\'")
print("'{:*^20s}'".format(hw))

print(f"\'{hw:*^20s}\'")

# 格式化数字
x = 1.2345
print(f"{x:>10}|")
print(f"{x:<10}|")
# 默认 . 后面是精确位数
print(f"{x:^+10.3}|")
# 加了f后表示小数点后几位
print(f"{x:^+10.3f}|")
print(f"{x:$^10.2}|")
```

### 合并拼接字符串

将几个小的字符串合并为一个。
```python {cmd}
# 如果要合并的字符串时一个序列或iterable
parts = ['Is', 'Chicago', 'Not', 'Chicago']
parts_dic = {'a':'Is', 'd':'Chicago', 'b':'Not', 'c':'Chi'}
print(' '.join(parts))
print(' , '.join(parts))
print(''.join(parts))
print(' '.join(parts_dic))
# 用 + 连接
a = 'Is Chicago'
b = "Not Chicago"
print(a + ' ' + b)
# 两个字符串字面量直接并排合并
print('Hello' ' ' 'world')
```
编写字符串相关的程序时，要注意效率：
```python {cmd}
data = ['ACME', 50, 91.1]
# 不可取的做法, +操作符存在大量内存复制、垃圾回收
s = ''
for i in data:
    # + 操作存在
    s += str(i) + ','
print(s[:-1])
# 好一些的做法
s = ','.join(str(i) for i in data)
print(s)

a = '1'; b='b'; c='3'
# ugly mode
print(a + ':' + b + ':' +c)
# still ugle
print(':'.join([a,b,c]))
# better
print(a,b,c,sep=":")
```

当混合使用I/O操作和字符串操作时，有时候需要仔细研究你的程序：
```python
# Versino 1 : string connection
# 如果s1、s2较小时合适，i/o操作通常较慢
with open('filename') as f:
    f.write(s1 + s2)

# Version 2 : seperate I/O operations
# 如果s1、s2较大时合适，避免 + 的大量内存占用 
with open('filename') as f:
    f.write(s1)
    f.write(s2)
```

大量小字符串的输出：
```python {cmd}
def sample():
    yield 'Is'
    yield 'Chicago'
    yield 'Not'
    yield 'Chicago'

print(' '.join(sample()))
for i in sample():
    print(i)
```

### 字符串中的变量替换
```python {cmd}
name = 'Fred'
weight = 71.32
# 原始版本
print('I am %s, weight is %.1f' %(name, weight))
# f 字符串版本
print(f'I am {name} ,weight is {weight:#^10.1f}' %vars())
```

### html 和 xml 的转换

```python
import html
# 将html中的特殊字符进行转换，使其能够在html中显示
text = 'This is <strong> "strong text" </strong>'
print(text)
print(html.escape(text))
# html.escape的第二个参数quote指示是否替换冒号
print(html.escape(text, quote=False))
# html.escape 的反过程
print(html.unescape('This is &lt;strong&gt; &quot;strong text&quot; &lt;/strong&gt;'))
```
```
This is <strong> "strong text" </strong>
This is &lt;strong&gt; &quot;strong text&quot; &lt;/strong&gt;
This is &lt;strong&gt; "strong text" &lt;/strong&gt;
This is <strong> "strong text" </strong>
```

### 字符串令牌解析
目标：将`text = 'foo = 23 + 42 * 10'`
解析为：`   tokens = [('NAME', 'foo'), ('EQ','='), ('NUM', '23'), ('PLUS','+'),
          ('NUM', '42'), ('TIMES', '*'), ('NUM', '10')]`

```python {cmd}
import re
# 定义令牌
NAME = r'(?P<NAME>[a-zA-Z][a-zA-Z0-9_]*)'
NUM = r'(?P<NUM>\d+)'
PLUS = r'(?P<PLUS>\+)'
TIMES = r'(?P<TIMES>\*)'
EQ = r'(?P<EQ>=)'
WS = r'(?P<WS>\s+)'
master_pat = re.compile('|'.join([NAME, NUM, PLUS, TIMES, EQ, WS]))
# 使用scanner进行扫描 方法1
print("{:*^30s}".format(' scanner method 1 '))
scanner = master_pat.scanner('foo = 42')
while True:
    m = scanner.match()
    if m:
        print((m.lastgroup, m.group()))
    else:
        break
print('{:*^30s}'.format(' scanner method 2 '))
# 方式2
scanner = master_pat.scanner('foo = 42')
for m in iter(scanner.match, None):
    print((m.lastgroup, m.group()))
# 方式3
print('{:*^30s}'.format(' scanner method 3 '))
from collections import namedtuple
def generate_token(pat, text):
    Token = namedtuple('Token', ['type','value'])
    scanner = pat.scanner(text)
    for m in iter(scanner.match, None):
        yield Token(m.lastgroup, m.group())
for tok in generate_token(master_pat, 'foo = 42'):
    print(tok)
# 过滤生成的符号
print('{:*^30s}'.format(' filter the tokens '))
tokens = (t for t in generate_token(master_pat, 'foo = 42') 
    if t.type!='WS')
for t in tokens:
    print(t)
```
通常来讲，令牌化是很多高级文本解析与处理的第一步，为了使用上面的扫描方法，你需要记住：
1. 你必须确认你使用正则表达式指定了所有输入中可能出现的文本序列，如果有任何不可匹配的文本出现，扫描就会直接停止；
2. 令牌顺序也是有影响的，`re` 模块会按照指定好的顺序去做匹配，因此如果一个模式恰好是另一个更长模式的子字符串，你需要将长模式写在前面，如：
```python
import re
LT = r'(?P<LT><)'
LE = r'(?P<LE><=)'
EQ = r'(?P<EQ>=)'
# incorrect method
print('{:*^30s}'.format('incorrect method'))
master_pat = re.compile('|'.join([LT, LE, EQ]))
scanner = master_pat.scanner('<=')
for m in iter(scanner.match, None):
    print((m.lastgroup, m.group()))
print('{:*^30s}'.format('correct method'))
master_pat = re.compile('|'.join([LE, LT, EQ]))
scanner = master_pat.scanner('<=')
for m in iter(scanner.match, None):
    print((m.lastgroup, m.group()))
```
```
*******incorrect method*******
('LT', '<')
('EQ', '=')
********correct method********
('LE', '<=')
```
3. 留意子字符串形式的模式，比如你有以下两个模式：
```python {cmd}
import re
PRINT = r'(?P<PRINT>print)'
NAME = r'(?P<NAME>[a-zA-Z_][a-zA-Z0-9_]*)'
master_pat = re.compile('|'.join([PRINT, NAME]))
scanner = master_pat.scanner('printer')
for m in iter(scanner.match, None):
    print((m.lastgroup, m.group()))
```
更高阶的令牌化技术可能需要查看 *PyParsing* 或者 *PLY* 包。

### 一个简单的递归下降分析器

要求：根据一组语法规则解析文本并执行命令，或者构造一个代表输入的抽象语法树，如果语法非常简单，你可以自己写这个解析器，而不是使用一些框架。

解决方案：
在这个问题中，我们集中讨论特殊语法去解析文本的问题。为了这样做，你首先要以BNF或EBNF形式指定一个标准语法，比如，一个简单数学表达式语法可能像下面：
```
expr ::= expr + term
    | expr - term
    | term
term ::= term * factor
    | term /  factor
    | factor
factor ::= (expr)
    | NUM
```
或者，以EBNF形式：
```
expr ::= term {(+|-) term}*
term ::= factor {(*|/) factor}*
factor ::= (expr)
    | NUM
```
在EBNF中，包含在 {...}* 中的规则是可选的。* 表示0次或多次重复。

对于复杂的语法，你最好是选择某个解析工具，如PyParsing 或者是 PLY，下面是使用PLY写的表达式求值程序：
```python {cmd}
from ply.yacc import yacc
from ply.lex import lex

# Token list
tokens=['NUM','PLUS','MINUS','TIMES','DIVIDE','LPAREN','RPAREN']
# Ignored characters
t_ignore = ' \t\n'
# Token specifications (as regexs)
t_PLUS = r'\+'
t_MINUS = r'\-'
t_TIMES = r'\*'
t_DIVIDE = r'/'
t_LPAREN = r'\('
t_RPAREN = r'\)'

# Token processing functions
def t_NUM(t):
    r'\d+'
    t.value = int(t.value)
    return t

# error handler
def t_error(t):
    print('Bad character: {!r}'.format(t.value[0]))
    t.skip(1)

# bulid the lexer
lexer = lex()

# Grammer rules and handler functions
def p_expr(p):
    '''
    expr : expr PLUS term
        | expr MINUS term
    '''
    if p[2] == '+':
        p[0] = p[1] + p[3]
    elif p[2] == '-':
        p[0] = p[1] - p[3]

def p_expr_term(p):
    '''
    expr : term
    '''
    p[0] = p[1]

def p_term(p):
    '''
    term : term TIMES factor
        | term DIVIDE factor
    '''
    if p[2] == '*':
        p[0] = p[1]*p[3]
    elif p[2] == '/':
        p[0] = p[1]/p[3]

def p_term_factor(p):
    '''
    term : factor
    '''
    p[0] = p[1]

def p_factor(p):
    '''
    factor : NUM
    '''
    p[0] = p[1]

def p_factor_group(p):
    '''
    factor : LPAREN expr RPAREN
    '''
    p[0] = p[2]

def p_error(p):
    print('Syntax error')

parser = yacc()

print(parser.parse('2'))
print(parser.parse('2+3'))
print(parser.parse('2*(3+4) + 5'))
```

### 字节字符串上的字符串操作
```python {cmd}
# 字节字符串同样也支持大部分和文本字符串一样的内置操作
data = b'Hello World'
print(data[:5])
print(data.startswith(b'Hello'))
print(data.split())
print(data.replace(b'Hello', b'Hello Cruel'))
# 这些操作同样适用于字节数组
data = bytearray(b'Hello World')
print(data)
print(data.startswith(b'Hello'))
print(data.split())
print(data.replace(b'Hello', b'Hello Curel'))

# 可以用正则表达式匹配字节字符串
data = b'FOO:BAR,SPAM'
import re
print(re.split(b'[:,]+', data))

# 大多数情况下，在文本字符串上的操作均可以用于字节字符串
# 不同点1：字节字符串索引操作返回整数而不是单个字符
a = 'Hello World'
b = b'Hello World'
print(f'{a[0]} , 0x{b[0]:x}')
print(a[0], b[0])
# 不同点2：打印出来为 b'...'
print(b)
print(b.decode('ascii'))
# 不同点3：不能格式化
try:
    print(b'{:x}'.format(12))
except:
    print('catch a except')
# 实现的方式
print('{:x}'.format(12).encode('ascii'))
```
一些程序员为了提升程序执行的速度，会倾向于使用字节字符串而不是我蹦字符串，尽管操作字节字符串确实会比文本更加高效（因为厂里文本固有的Unicode相关开销），但通常会导致代码杂乱。你会经常发现字节字符串并不能和Python的其他部分工作，需要手动 encode/decode 操作。


## 数字日期和时间

在Python中执行整数和浮点数的数字运算是很简单。如果你需要执行分数、数组或者是日期和时间的运算，就需要做更多工作。

### 数字的四舍五入
```python {cmd}
print(round(1.24,1))
# 当一个值更好在两个边界，round函数返回离它最近的偶数
print(round(1.5,0))
print(round(2.5,0))
# round(number [,ndigits]), ndigits可以为负，表示小数前几位
print(round(12345, -3))
# 如果仅仅是为了显示，用str.format
print('{:-.0f}'.format(1.5))
# 不要试着去舍入浮点来“修正”表面上看起来正确的问题
a = 2.1
b = 4.2
c = a + b
print(c)
c = round(c, 2)
print(c)
```
对于大多数使用到浮点的程序，没有必要也不推荐怎么做，尽管在计算的时候回有一点点小的误差，但是这些小的误差是能被理解和容忍的。如果不能允许这些小误差（比如涉及到金融领域），就需要考虑使用 decimal 模块了。

### 执行精确的浮点数运算

浮点数的一个普遍问题是它们不能精确表示十进制数，并且即使是最简单的数学运算也会产生小的误差：
```python {cmd}
a = 0.2
b = 0.1
c = a + b
print(c)
print(c==0.3)
# 正确的判断
import math
print(math.isclose(c, 0.3))
```
这些错误是有底层CPU和IEEE754标准通过自己的浮点单元去执行算数时的特征。由于Python的浮点数据类型使用底层表示存储数据，因此你没办法去避免这样的误差。

如果你想更加精确（并能容忍一定的性能损耗），你可以使用decimal模块：
```python {cmd}
from decimal import Decimal
from decimal import localcontext
a = Decimal('4.2')
b = Decimal('2.1')
print(a+b)
print(a+b==Decimal('6.3'))
a = Decimal('1.3')
b = Decimal('1.7')
print(a/b)
with localcontext() as ctx:
    ctx.prec = 3
    print(a/b)
with localcontext() as ctx:
    ctx.prec = 7
    print(a/b)
```

decimal 模块实现了 IBM 的“通用小数运算规范”。

Python新手会倾向于使用decimal模块来处理浮点数的精确运算。然而，先理解你的应用程序目的是非常重要的。如果你是在做科学计算或者工程领域的计算、电脑绘图或者是科学领域的大多数运算，那么使用普通的浮点类型是比较普遍的用法。其中的一个原因是，在真实的世界中很少会要求精确到普通浮点数能提供的17位精度。第二个原因是，原生的浮点数计算要快的多。

误差的产生：
```python {cmd}
a = 1.23e+18
b = 1
c = -1.23e+18
# 1 被“丢失了”
print(a+b+c)
# 使用 math.fsum 进行精确运算
from math import fsum
print(fsum([a,b,c]))
```
### 数字的格式化输出

```python {cmd}
x = 1234.56789
print(f"'{x:.2f}'")
print(f"'{x:>10.1f}'")
print(f"'{x:<10.1f}'")
print(f"'{x:^10.1f}'")
print(f"'{x:,}'")
print(f"'{x:e}'")
print(f"'{x:.3E}'")
# 同时制定宽度和精度的一般形式是：[<>^]?width[,]?(.digits)?
print(f"'{x:,.2f}'")
#上述方法同样使用与Decimal
from decimal import Decimal
x = Decimal('1234.56789')
print(f'{x:*^15,.3f}')
```

### 二八十六进制表示整数
```python {cmd}
x = 1234
print(bin(x))
print(f'{x:b}')

print(oct(x))
print(f'{x:o}')

print(hex(x))
print(f'{x:x}')

# 整数是有符号的
x = -x
print(bin(x))
print(f'{x:b}')
# 显示负数对应的无符号二进制表示
# 32 位对应的无符号表示
print(f'{2**32+x:b}')
print(f'{2**32+x:x}')

# 将不同进制字符串转换为整数
print(int('4d2', 16))
print(int('10011010010', 2))
```

### 字节到大整数的打包与解包
```python {cmd}
data = b'\x00\x124V\x00x\x90\xab\x00\xcd\xef\x01\x00#\x004'
print(len(data))
print(f"{int.from_bytes(data, 'little'):x}")
print(f"{int.from_bytes(data, 'big'):x}")
print(int.from_bytes(data, 'big'))

x = 94522842520747284487117727783387188
print(x.to_bytes(16, 'big'))
```
大整数和字节字符串之间的转换操作并不常见，然而，在一些应用领域，比如密码学或网络，有时候也会出现。

### 复数

```python {cmd}
a = complex(2,4)
b = 2+4j
print(a)
print(a==b)
print(a.real, a.imag)
# 共轭复数
print(a.conjugate())
```
常见的运算可以工作：
```python {cmd}
a = 2+4j
b = 1-3j
print(a+b)
print(a-b)
print(a*b)
print(a/b)
```
Python 中大部分与数字相关的模块都能处理复数，比如如果使用numpy，可以很容易构造一个复数数组并在这个数组上执行各种操作：
```python {cmd}
import numpy as np
a = np.array([2+3j,4+5j,6-7j,8+9j])
print(a)
print(a+2)
print(np.sin(a))
```
Python的标准数学函数模块 math 不能产生复数值，因此你的代码不会出现复数返回值：
```python {cmd}
import math
print(math.sqrt(-1))
```
但是可以使用 cmath 模块：
```python {cmd}
import cmath
print(cmath.sqrt(-1))
# 或者是 ** 运算符
print((-1)**.5)
```

### 无穷大与NaN

Python并没有特殊的语法来表示正无穷、负无穷、NaN(非数字)的浮点数，但是可以使用`float()`来创建它们：
```python {cmd}
import math
a = float('inf')
b = float('-inf')
c = float('nan')
print(a) # inf
print(math.isinf(a)) # True
print(b) # -inf
print(math.isinf(b)) # True
print(c) # nan
print(math.isnan(c)) # True
# 无穷大的运算
print(a+100) # inf
print(a*100) # inf
print(10000/a) # 0.0
print(a/a) # nan
print(a+b) # nan
# NaN会在所有操作中传播
print(c + 20) # nan
print(c / 20) # nan
# 两个NaN值不相等
print(c==float('nan')) # False
```

### 分数运算

`fractions` 模块可以被用来执行包含分数的数学运算，比如：
```python {cmd}
from fractions import Fraction
a = Fraction(5, 4)
b = Fraction(7, 16)
print(a+b) # 27/16
print(a*b) # 35/64

# 获取 numerator / denominator
c = a * b
print(c.numerator) # 35
print(c.denominator) # 64

# 转换为小数
print(float(c)) # 0.546875

# 限制分母大小
print(c.limit_denominator(8)) # 4/7

# 小数转换为分数
x = 3.75
print(Fraction(*x.as_integer_ratio()))
```
在大多数程序中一般不会出现分数的计算问题，但是有时候还是需要用到的。比如在一个允许接收分数形式的测试单位以分数形式执行运算的程序中，直接使用分数可以减小手动转换为小数或浮点数的工作。

### 大型数组运算

涉及到数组的重量级运算操作，可以使用NumPy库。NumPy的一个主要特征是它会给Python提供一个数组对象，相比于标准的Python列表而言，更适合用来做数学运算。

传统的 list 操作：
```python {cmd id="20180408214923"}
# Python list
x = [1, 2, 3, 4]
y = [5, 6, 7, 8]
```
```python {cmd continue="20180408214923"}
print(x * 2)
```
```python {cmd continue="20180408214923"}
print(x+10)
```
```python {cmd continue="20180408214923"}
print(x+y)
```

Numpy 库的操作：
```python {cmd id="20180408215711"}
import numpy as np
ax = np.array([1,2,3,4])
ay = np.array([5,6,7,8])
```
```python {cmd continue="20180408215711"}
print(ax*2)
```
```python {cmd continue="20180408215711"}
print(ax+10)
```
```python {cmd continue="20180408215711"}
print(ax+ay)
```
```python {cmd continue="20180408215711"}
print(ax*ay)
```

如上所示，两种方案中数组的基本数学运算结果并不相同。特别地，NumPy中的标量运算（比如 ax*2 或 ax+10）会作用在每一个元素上，另外当两个操作都是数组的时候执行元素对等位置计算，并最终生成一个新的数组。

对这个数组中所有元素同时执行数学运算可以使得作用在数组上的函数简单而快速。比如，你想计算多项式的值：
```python {cmd continue="20180408215711"}
def f(x):
    return 3*x**2 - 2*x + 7
print(f(ax))
```

NumPy还为数组操作提供了大量的通用函数，这些函数可以作为math模块中的替代，比如：
```python {cmd continue="20180408215711"}
print(np.sqrt(ax))
print(np.cos(ax))
```
使用这些通用函数要比循环数组并使用 math 模块中的函数执行计算要快得多。因此只要有可能，尽量选择 NumPy 的数组方案。

底层实现中，NumPy 数组使用了 C 或者 Fortran 语言的机制分配内存。也就是说，它们是一个非常大的连续的并且有相同类型数据组成的内存区域。所以，你可以构造一个比普通 Python 列表大得多的数组，比如，你构建一个10000*10000的浮点数二维数组：
```python {cmd id="20180408221348"}
import numpy as np
grid = np.zeros(shape=(10000, 10000), dtype=float)
```
```python {cmd continue="20180408221348"}
print(grid)
```
所有普通操作都会作用在所有元素上：
```python {cmd continue="20180408221348" id="20180408221653"}
grid += 10
```
```python {cmd continue="20180408221653"}
print(grid)
```
```python {cmd continue="20180408221653"}
print(np.sin(grid))
```

关于 NumPy ，有一点需要特别地注意，那就是他扩展了Python列表的索引功能：
```python {cmd}
import numpy as np
a = np.array([[1,2,3,4],[5,6,7,8],[9,10,11,12]])
print(a, end='\n'*2)
print(a[1], end='\n'*2)
print(a[:, 1], end='\n'*2)
a[1:,1:3] += 10
print(a, end='\n'*2)
print(a+[100, 101, 102, 103], end='\n'*2)
print(a+[[100],[101],[102]], end='\n'*2)
print(np.where(a<10, a, 10))
```
NumPy 是 Python 领域中很多科学与工程库的基础，同时也是被广泛使用的最大最复杂的模块。

### 矩阵与线性代数运算

你需要执行矩阵和线性代数运算，比如矩阵乘法、寻找行列式、求解线性方程组等等。

NumPy 库有一个矩阵对象可以用来解决这个问题。矩阵类似于3.9小节中数组对象，但是遵循线性代数的计算规则：
```python {cmd}
import numpy as np
m = np.matrix([[1, -2, 3], [0, 4, 5], [7, 8, -9]])
print(m, end='\n'*2)
print(m.T, end='\n'*2)
# 返回矩阵的逆
print(m.I, end='\n'*2)
# m·m.I = identity matrix
print(m*m.I, end='\n'*2)

v = np.matrix([[2],[3],[4]])
print(v, end='\n'*2)

print(m*v)
```

可以在 `numpy.linalg`子包中找到更多的操作函数：
```python {cmd}
import numpy as np
# determinant 矩阵行列式的值
m = np.matrix([[1, -2, 3], [0, 4, 5], [7, 8, -9]])
v = np.matrix([[2],[3],[4]])
print(np.linalg.det(m), end='\n'*2)
# eigenvalues 特征值
print(np.linalg.eigvals(m), end='\n'*2)
# 解方程 mx=v
x = np.linalg.solve(m, v)
print(x, end='\n'*2)
print(m*x, end='\n'*2)
```

### 随机选择

从一个序列中随机抽取若干个元素，或者想生成几个随机数：
```python {cmd}
import random
values = [1,2,3,4,5,6]
# 随机选出 list 中的一个元素
print(random.choice(values))
print(random.choice(values))
# 随机选出 list 中的若干个元素
print(random.sample(values, 3))
# 打乱序列中元素的顺序
random.shuffle(values)
print(values)
# 随机生成整数，范围为 [0, 100] 的闭区间
print(random.randint(0,100))
# 随机生成 0~1 范围内的浮点数
print(random.random())
# 以等概率均匀分布生成 0~1 的随机数
print(random.uniform(0, 1))
# 以均值为0，标准差为1，生成高斯分布
print(random.gauss(0, 1))
# 获取 N 为随机位（二进制）的整数
print(f'{random.getrandbits(200):x}')
```

random 模块使用 Mersenne Twister 算法来计算生成随机数，这是一个确定性算法，但你可以通过 random.seed() 函数修改初始化种子。

### 基本日期与时间转换

与日期、时间相关的模块为 `datetime`。其子模块 `timedelta` 用于表示时间间隔：
```python {cmd}
# timedelta 表示一段时间间隔
from datetime import timedelta
a = timedelta(days=2, hours=14)
b = timedelta(hours=17.5)
c = a+b
print(c.days)
print(c.seconds)
print(c.seconds/3600)
print(c.total_seconds() / 3600)
```

如果你想表示指定的日期和时间，先创建一个 datetime 实例，然后使用标准的数学运算来操作它们：
```python {cmd}
from datetime import datetime
from datetime import timedelta
a = datetime(2018, 4, 9, 10, 49, 55)
# datetime 和 timedelta 的运算
print(a+timedelta(hours=10, minutes=5))
b = datetime(2012, 12,21)
# datetime 之间的运算
print(a-b)
# 获取当前时间
now = datetime.now()
print(now)
```

注意：datetime 会自动处理闰年：
```python {cmd}
from datetime import datetime
a = datetime(2012,3,1)
b = datetime(2012,2,28)
print(a-b)
a = datetime(2100,3,1)
b = datetime(2100,2,28)
print(a-b)
```

对于大多数基本的日期和时间处理问题，`datetime` 模块已经足够了，如果你需要执行更加复杂的日期操作，如处理时区、节假日计算等，可以考虑使用 `dateutil` 模块。

### 计算最后一个周五的日期

```python {cmd}
from datetime import datetime, timedelta
weekdays = ['Monday', 'Tuesday', 'Wednesday',
    'Thursday', 'Friday', 'Saturday', 'Sunday']
def get_previous_byday(dayname, start_date=None):
    if start_date==None:
        start_date = datetime.today()
    day_delta = (7+start_date.weekday() -
        weekdays.index(dayname))%7
    day_delta = day_delta if day_delta else 7
    return start_date-timedelta(days=day_delta)
print(get_previous_byday('Sunday'))

# 2016年最后一个星期五
print(get_previous_byday('Friday', datetime(2017, 1, 1)))
```
查看真实情况：
```sh {cmd="/bin/zsh"}
cal 12 2016
```
可见2016年最后一个周五真的是：2016-12-30。

如果要执行大量的日期计算，最好使用模块 `dateutil`。
```python {cmd}
from datetime import datetime
from dateutil.relativedelta import relativedelta
from dateutil.rrule import *
d = datetime(2017, 1, 1)
print(d+relativedelta(weekday=FR(-1)))
# 修改day属性
print(d.replace(day=10))
```

calendar 用于与日历相关的信息处理：
```python {cmd}
import calendar
# 返回 2017-2-1 星期几，共多少天
print(calendar.monthrange(2017, 2)) # 2表示周三
# 返回 2016-2-1 星期几，共多少天
print(calendar.monthrange(2016, 2)) # 0表示周一
```

### 字符串转换为时间
```python {cmd}
from datetime import datetime
text = '2018-4-9'
y = datetime.strptime(text, '%Y-%m-%d')
print(y)
print(datetime.strftime(y, '%A %B %d, %Y'))
```
注意：`strptime`的性能非常差，如果知道解析格式，最好自己写：
```python {cmd}
from datetime import datetime
def strptime(s):
    year_s, month_s, day_s = s.split('-')
    return datetime(int(year_s), int(month_s),int(day_s))
print(strptime('2018-4-9'))
```
经过测试，自定义的函数性能比`datetime.strptime`快7倍，尽量使用自定义的时间解析。

## 迭代器与生成器

### 手动遍历迭代器(不用 for)
有如下的一个迭代器：
```python {cmd id="20180409155152"}
a = [1,2,3,4,5]
iter_a = iter(a)
```
使用`next`函数进行遍历。

遍历方式1：
```python {cmd continue="20180409155152"}
try:
    while True:
        v = next(iter_a)
        print(v)
except StopIteration:
    print('stop iteration')    
```
从上面的例子中可以看出，结束时，会弹出一个`StopIteration`异常。

遍历方式二：
```python {cmd continue="20180409155152"}
while True:
    v = next(iter_a, None)
    if v:
        print(v)
    else:
        break
```

### 代理迭代

创建一个对象能够用于迭代。
```python {cmd}
class Node:
    def __init__(self, value):
        self._value = value
        self._children = []
    def __repr__(self):
        return f'Node({self._value})'
    def add_child(self, node):
        self._children.append(node)
    def __iter__(self):
        return iter(self._children)
root = Node(0)
child1 = Node(1)
child2 = Node(2)
root.add_child(child1)
root.add_child(child2)
for ch in root:
    print(ch)
```

### 使用生成器创建新的迭代模式

实现一个自定义迭代模式，跟普通的内置函数，比如`range()`、`reversed()`不一样。

```python {cmd}
def frange(start, stop, increment):
    x = start
    while x < stop:
        yield x
        x += increment
for i in frange(0, 4, 0.5):
    print(i, end='  ')
print('\n', sum(frange(1,5,1)))
print(list(range(0, 5, 1)))
```

一个函数中需要有一个 `yield` 语句即可将其转换为一个生成器。跟普通函数不同的是，生成器只能用于迭代操作。下面是一个实验，向你展示这样的函数底层工作机制：

```python {cmd}
def countdown(n):
    print('Starting to count from', n)
    while n > 0:
        yield n
        n -= 1
    print('Done!')
c = countdown(3)
print(next(c))
print(next(c))
print(next(c))
print(next(c))
```

### 实现迭代器协议



