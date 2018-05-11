---
export_on_save:
 html: ture
---

@import "/blog_head.md"

# 超越经典搜索

与 BFS、DFS、A\* 等经典搜索算法不同，下面介绍的方法是不知道目标状态，需要求解该状态。而不是已知目标状态，求到达该状态的一条路径。

## 爬山法

用爬山法获得8皇后问题的一个解。

### 回顾八皇后问题的求所有解

首先看一下使用普通的回溯法求解八皇后问题：
```python
def get_empress_pos(n):
    a = [-1]*n
    puted = set()
    pos = 0
    while pos>=0:
        for i in range(a[pos]+1, n):
            if i in puted:
                continue
            for j in range(pos):
                if abs(j-pos)==abs(a[j]-i):
                    break
            else:
                if a[pos]!= -1:
                    puted.remove(a[pos])
                puted.add(i)
                a[pos] = i
                pos += 1
                if pos==n:
                    yield a
                    pos -=1
                break
        else:
            if a[pos]!= -1:
                puted.remove(a[pos])
            a[pos] = -1
            pos -= 1
result = [x[:] for x in get_empress_pos(8)]
len(result)
```
结果为：92 种布局，性能为：
```
6.24 ms ± 18.7 µs per loop (mean ± std. dev. of 7 runs, 100 loops each)
```

还可以用普通的枚举法：
```python
from itertools import permutations
def enum_empress_pos(n):
    for seq in permutations(range(n)):
        for i in range(n):
            for j in range(i+1, n):
                if abs(i-j)!=abs(seq[i]-seq[j]):
                    continue
                break
            else:
                continue
            break
        else:
            yield seq
```
结果也是92种，性能为：
```
63.5 ms ± 398 µs per loop (mean ± std. dev. of 7 runs, 10 loops each)
```

### 使用 LV+backtrace 求解一个解

使用拉斯维加斯算法+回溯算法求解一个100皇后问题的可能解（不具备完备性）：
```python
import random
def judge_conflict(a, v):
    v_p = len(a)
    for i in range(len(a)):
        if v_p-i==abs(v-a[i]):
            return True
    return False
def LvBk_empress(n):
    a = [-1]*n
    unselected = set(range(n))
    # Partition of LV
    pos = 0
    while pos < n//3:
        for i in range(n):
            v = random.sample(unselected, 1)[0]
            if judge_conflict(a[:pos], v)==False:
                unselected.remove(v)
                a[pos] = v
                pos += 1
                break
        else:
            break
    # Partition of Backtrace
    while pos>=0:
        for i in range(a[pos]+1, n):
            if i not in unselected:
                continue
            if not judge_conflict(a[:pos], i):
                if a[pos]!= -1:
                    unselected.add(a[pos])
                unselected.remove(i)
                a[pos] = i
                pos += 1
                if pos==n:
                    return a
                break
        else:
            if a[pos]!= -1:
                unselected.add(a[pos])
            a[pos] = -1
            pos -= 1

%time LvBk_empress(40)
```
结果为：
```
CPU times: user 12.8 s, sys: 10.9 ms, total: 12.8 s
Wall time: 12.9 s
[out] : 
[18, 10, 6, 29, 12, 38, 36, 3, 27, 14,
7, 22, 35, 0, 5, 9, 4, 17, 8, 2,
19, 1, 23, 30, 34, 37, 39, 33, 26, 32,
25, 20, 16, 11, 15, 21, 28, 13, 31, 24]
```
### 使用爬山法求解一个解

```python
from collections import Counter

# 测试攻击相互攻击的分数，结果越低越好
def calc_height(state):
    count1,count2 = Counter(), Counter()
    for k in range(len(state)):
        count1.update([k-state[k]])
        count2.update([k+state[k]])
    h = 0
    for k in count1.values():h+=k-1
    for k in count2.values():h+=k-1
    return h

def lowest_height(cur_state):
    lowest_state = None
    lowset_v = None
    for i in range(len(cur_state)):
        for j in range(i+1,len(cur_state)):
            ts = cur_state[:]
            ts[i],ts[j] = ts[j],ts[i]
            h = calc_height(ts)
            if lowset_v==None or lowset_v>h:
                lowset_v = h
                lowest_state = ts
            if lowset_v==0:
                break
    return lowset_v, lowest_state

def hill_climbing_empress(n, get_lowh):
    cur_state = random.sample(range(n), n)
    low_v = None
    while low_v==None or low_v!=0:
        tp_v, tp_s = get_lowh(cur_state, low_v)
        if tp_v==None:break
        if low_v==None or low_v>tp_v:
            low_v, cur_state = tp_v, tp_s
        else:
            break
    print(low_v, cur_state)
    
%time hill_climbing_empress(40, lowest_height)
```
结果为：
```
0 [23, 18, 4, 24, 20, 35, 26, 2, 22, 1, 38, 15, 3, 9, 11, 16, 34, 0, 
21, 27, 31, 7, 32, 29, 5, 37, 39, 36, 8, 14, 17, 30, 25, 12, 10, 6, 
13, 19, 28, 33]
CPU times: user 1.15 s, sys: 5.94 ms, total: 1.16 s
Wall time: 1.18 s
```
可见，速度比之前的 LV+Backtrace 算法快很多,不过也不能一概而论，LV+Backtrace 算法非常不稳定，有时候几毫秒就完成了，有时候需要几分钟。

而相对而言，爬山法一直很稳定的在2秒以内，不过有时候得到的是局部最优解，不过多算两次就能得到正确的解，相对而言爬山法更好一些。

爬山法有时候也被称为贪婪局部搜索，因为它只选择邻居中状态最好的一个，而不考虑下一步该如何走，但是贪婪算法却非常有效。

贪婪算法很快朝着解的方向进展，因为它很容易改善一个坏的状态。

爬山法又有很多的变形，变换在于选择上山的路径上：
- **随机爬山法**：在上山移动中随机选择下一步，被选中的概率随着上山移动的陡峭程度不同而不同；
- **首选爬山法**：随机地生成后继节点直到生成一个优于当前节点的后继，这个算法在后继节点很多的时候是个好策略。

下面使用首选爬山法对其进行测试：
```python
def lower_height(cur_state, pre_height):
    for i in range(len(cur_state)):
        for j in range(i+1,len(cur_state)):
            ts = cur_state[:]
            ts[i],ts[j] = ts[j],ts[i]
            h = calc_height(ts)
            if pre_height==None or pre_height>h:
                return h, ts
    return None, None
%time hill_climbing_empress(40, lower_height)
```
其结果为：
```
0 [25, 9, 15, 0, 30, 35, 10, 22, 20, 28, 12, 16, 26, 8, 4, 31, 39, 
27, 1, 11, 38, 2, 29, 3, 17, 7, 13, 36, 34, 6, 18, 5, 21, 24, 32, 
19, 14, 23, 37, 33]
CPU times: user 413 ms, sys: 3.41 ms, total: 417 ms
Wall time: 427 ms
```
将时间限制在了半秒以内，性能大大提升！而且当 n 的值越大，首选爬山法的优势越明显。该方法获得 100 皇后的一个解也就用了 6 s 左右。

NP难解通常有指数级数目的局部极大值。尽管如此，经过少数随机重启的搜索之后还是能找到一个合理的较好的局部最大值。

### 测试一次成功的准确率

```python
c = Counter(hill_climbing_empress(16, lower_height) 
    for i in range(200))
print(c, '\n', c[0]/sum(c.values()))

c = Counter(hill_climbing_empress(16, lowest_height) 
    for i in range(200))
print(c, '\n', c[0]/sum(c.values()))
```
```
Counter({1: 108, 2: 55, 0: 36, 3: 1}) 
 0.18

Counter({1: 91, 0: 60, 2: 45, 3: 4}) 
 0.3
```
通过类似的多次试验也发现，使用选择最优爬山法比首选爬山法的一次成功的可能性大一点，不过两者都不是很高。而 LV+Backtrace 的算法的一次准确率几乎为1。

### 随机重启爬山法

为了保证函数调用成功的概率为1，可以使用随机重启爬山法，即如果在非最优时结束，则重新初始化进行计算：
```python
def stochastic_reset_empress(n, get_lowh):
    for i in range(10):
        low_v = hill_climbing_empress(n, get_lowh)
        if low_v==0:
            break
    return low_v
%time stochastic_reset_empress(50, lower_height)
```
结果为：
```python
CPU times: user 4.97 s, sys: 9.91 ms, total: 4.98 s
Wall time: 5 s
[out] : 0
```
现在解决了可能返回失败的情况，不过性能比之前的几百毫秒低很多，那能否保证正确性的同时，保证效率呢？

### 模拟退火搜索

```python
import math
from itertools import combinations
def simulated_annealing(n, speed=1):
    cur_state = random.sample(range(n), n)
    low_v = None
    successor_swap = list(combinations(range(n), 2))
    while low_v==None or low_v!=0:
        tp_swap = successor_swap[:]
        while len(tp_swap):
            r = random.sample(range(len(tp_swap)), 1)[0]
            p1, p2 = tp_swap[r]
            del tp_swap[r]
            tp_state = cur_state[:]
            tp_state[p1],tp_state[p2] = tp_state[p2],tp_state[p1]
            tp_v = calc_height(tp_state)
            if low_v==None or low_v>tp_v:
                low_v, cur_state = tp_v, tp_state
                break
            else:
                # 通过修改 random.uniform(0, speed) 中speed的值
                # 设置退火的速率，speed越大，求解速度越快，不过得到解的概率下降
                if random.uniform(0,speed) <= math.e**((low_v-tp_v)/low_v):
                    low_v, cur_state = tp_v, tp_state
                    break
    return low_v, cur_state

%time simulated_annealing(50, 100)[0]
```

结果为：
```
CPU times: user 2.57 s, sys: 5.94 ms, total: 2.58 s
Wall time: 2.58 s
[out] : 0
```
平均速度在2s左右，比之前的重启迭代深度要快很多，速度快是因为`speed`参数设置为了100，下面看看在`speed=100`时查找正确的概率：


