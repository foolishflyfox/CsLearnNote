---
export_on_save:
 html: true
---

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

### collections 模块

#### deque

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

## 获取全排列

```python {cmd}
import itertools
for i in itertools.permutations("abc"):
    print(i)
```
