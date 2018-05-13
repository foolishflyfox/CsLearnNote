---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 遗传算法计算旅行商问题

如下表所示，为一组中国城市的经纬度信息，现在要求从北京出发，旅行一圈，最后回到北京。这个过程中，同一个城市不能经过两次，两个城市之间的距离计算直接用经纬度作为坐标，求两者的欧拉距离即可：

|编号|城市名|东经|北纬|编号|城市名|东经|北纬|
|---|---|---|---|---|---|---|---|
|1|北京|116.46|39.92|18|南京|118.78|32.04
|2|天津|117.2|39.13|19|合肥|117.27|31.86
|3|上海|121.48|31.22|20|杭州|120.19|30.26
|4|重庆|106.54|29.59|21|福州|119.3|26.08
|5|拉萨|91.11|29.97|22|南昌|115.89|28.68
|6|乌鲁木齐|87.68|43.77|23|长沙|113|28.21
|7|银川|106.27|38.47|24|武汉|114.31|30.52
|8|呼和浩特|111.65|40.82|25|广州|113.23|23.16
|9|南宁|108.33|22.84|26|台北|121.5|25.05
|10|哈尔滨|126.63|45.75|27|海口|110.35|20.02
|11|长春|125.35|43.88|28|兰州|103.73|36.03
|12|沈阳|123.38|41.8|29|西安|108.95|34.27
|13|石家庄|114.48|38.03|30|成都|104.06|30.67
|14|太原|112.53|37.87|31|贵阳|106.71|26.57
|15|西宁|101.74|36.56|32|昆明|102.73|25.04
|16|济南|117|36.65|33|香港|114.1|22.2
|17|郑州|113.6|34.76|34|澳门|113.33|22.13

## 暴力枚举

```python
cities = [(116.46, 39.92),(117.2, 39.13),(121.48, 31.22),(106.54, 29.59),
          (91.11, 29.97),(87.68, 43.77),(106.27, 38.47),(111.65, 40.82),
          (108.33, 22.84),(126.63, 45.75),(125.35, 43.88),(123.38, 41.8),
          (114.48, 38.03),(112.53, 37.87),(101.74, 36.56),(117.0, 36.65),
          (113.6, 34.76),(118.78,32.04),(117.27,31.86),(120.19,30.26),
          (119.3,26.08),(115.89,28.68),(113.0,28.21),(114.31,30.52),
          (113.23,23.16),(121.5,25.05),(110.35,20.02),(103.73,36.03),
          (108.95,34.27),(104.06,30.67),(106.71,26.57),(102.73,25.04),
          (114.1,22.2),(113.33,22.13)]

from itertools import permutations
import math

# 使用枚举方式计算路径
def enum_TSP(cities):
    paths = permutations(range(1, len(cities)), len(cities)-1)
    shortest_path = None
    shortest_miles = None
    for path in paths:
        cur_miles = 0
        path = [0] + list(path)
        pre_pos = cities[path[-1]]
        for i in path:
            cur_miles += math.hypot(pre_pos[0]-cities[i][0],
                                   pre_pos[1]-cities[i][1])
            pre_pos = cities[i]
        if shortest_miles==None or shortest_miles>cur_miles:
            shortest_miles = cur_miles
            shortest_path = path
    return shortest_miles, shortest_path
import time
pre_running = 100000
# 测试随着城市个数的增加，计算时间的变化
for i in range(1,13):
    t1 = time.time()
    miles, path = enum_TSP(cities[:i])
    t2 = time.time()
    print("running:{:g}s x{:.3f}, miles:{}".format(
        t2-t1, (t2-t1)/pre_running, miles))
    pre_running = t2-t1

print(path)
```
测试结果为：
```
running:3.60012e-05s x0.000, miles:0.0
running:1.09673e-05s x0.305, miles:2.164901845350049
running:8.82149e-06s x0.804, miles:20.120564493919822
running:2.21729e-05s x2.514, miles:39.42664606580856
running:4.91142e-05s x2.215, miles:67.77227033726385
running:0.000365973s x7.451, miles:83.7957265568911
running:0.00200295s x5.473, miles:84.38275768767652
running:0.0180061s x8.990, miles:84.85443680089674
running:0.14526s x8.067, miles:92.40225788702355
running:1.16235s x8.002, miles:110.34594071307717
running:12.7086s x10.934, miles:110.43467064772815
running:148.912s x11.717, miles:110.6873206900153

[0, 1, 11, 10, 9, 2, 8, 3, 4, 5, 6, 7]
```
可以看出，随着节点个数的增加，时间复杂度不仅仅是以指数方式增大，而是以阶乘的方式增大。其中在有 12 个节点的时候用时就达到了149 s。

## 爬山法解TSP问题

```python
from itertools import combinations
def climb_TSP(cities):
    shortest_path = [0] + random.sample(range(1, len(cities)),
                                        len(cities)-1)
    shortest_miles = None
    while True:
        swap_poses = combinations(range(1, len(cities)), 2)
        for p1, p2 in swap_poses:
            tp_path = shortest_path[:]
            tp_path[p1],tp_path[p2]=tp_path[p2],tp_path[p1]
            tp_miles = 0
            pre_pos = cities[tp_path[-1]]
            for p in tp_path:
                tp_miles += math.hypot(pre_pos[0]-cities[p][0],
                                      pre_pos[1]-cities[p][1])
                pre_pos = cities[p]
            if shortest_miles == None or shortest_miles>tp_miles:
                shortest_path = tp_path
                shortest_miles = tp_miles
                break
        else:
            break
    return shortest_miles, shortest_path

pre_running = 100000
for i in range(1,len(cities)):
    t1 = time.time()
    miles2, path2 = climb_TSP(cities[:i])
    t2 = time.time()
    print("running:{:g}s x{:.3f}, miles:{}".format(
        t2-t1, (t2-t1)/pre_running, miles2))
    pre_running = t2-t1
    
print(path2)
```
结果为：
```
running:0.000128031s x0.000, miles:None
running:8.4877e-05s x0.663, miles:None
running:5.79357e-05s x0.683, miles:20.120564493919822
running:3.71933e-05s x0.642, miles:39.42664606580856
running:3.79086e-05s x1.019, miles:67.77227033726385
running:5.72205e-05s x1.509, miles:83.7957265568911
running:0.000101805s x1.779, miles:90.23282899625877
running:0.000200272s x1.967, miles:92.9941998469688
running:0.000232935s x1.163, miles:110.87183854597784
running:0.000420094s x1.803, miles:120.62660128330911
running:0.000742197s x1.767, miles:138.46718712299258
running:0.00130606s x1.760, miles:143.51666000899704
running:0.00225902s x1.730, miles:120.78177282473035
running:0.00282788s x1.252, miles:130.2100894627685
running:0.00347209s x1.228, miles:128.65765074239675
running:0.00546694s x1.575, miles:130.04736168607283
running:0.00604486s x1.106, miles:167.5561666681788
running:0.00989723s x1.637, miles:145.36418380202562
running:0.0146728s x1.483, miles:134.8147803710913
running:0.00706196s x0.481, miles:135.03955265179337
running:0.011061s x1.566, miles:154.7739169038902
running:0.0297201s x2.687, miles:147.92296705350185
running:0.00878906s x0.296, miles:156.82077414333395
running:0.0464067s x5.280, miles:161.4439643821685
running:0.0416319s x0.897, miles:159.0443171682327
running:0.022413s x0.538, miles:168.03001602322684
running:0.0185859s x0.829, miles:189.1730535664609
running:0.0593841s x3.195, miles:156.24074812942845
running:0.0345321s x0.582, miles:183.37720511542645
running:0.0417631s x1.209, miles:198.23621578039857
running:0.114813s x2.749, miles:176.3954187817255
running:0.0456481s x0.398, miles:194.21122328178666
running:0.105248s x2.306, miles:184.54587357258475
[0, 11, 10, 9, 7, 6, 5, 4, 14, 27, 29, 31, 8, 26, 22, 23, 21, 18, 
17, 15, 1, 12, 13, 16, 28, 3, 30, 24, 32, 20, 25, 19, 2]
```
虽然并不是最优的解，但是速度非常快。用枚举法解全部的34个城市的最短路径，总共有 $33!/2$中情况，一共大约有$4.34\times 10^{36}$中情况，这要迭代非常长的时间，而使用爬山法几乎是瞬间解决。并且在测试中也能看出，耗时时间随城市个数的增多并没有明显的变化！

### 局部束搜索解TSP问题

为了提高准确性，可以使用局部束搜索。

局部束搜索(local beam search)算法记录k个状态而不是只记录一个。它从k个随机生成的状态开始，每一步全部k个状态的所有后继全部被生成。如果其中有一个是目标状态，算法停止，否则从整个后继列表中选择k个最佳的后继，重复该过程：
```python
from random import sample
from collections import namedtuple
from itertools import combinations
import math
import time
def genetic_lbs(cities):
    class BeamNode:
        def __init__(self, miles, path, best):
            self.miles, self.path, self.best = miles, path, best
    def calc_miles(path):
        pre_pos = cities[path[-1]]
        tp_miles = 0
        for p in path:
            tp_miles += math.hypot(pre_pos[0]-cities[p][0],
                                  pre_pos[1]-cities[p][1])
            pre_pos = cities[p]
        return tp_miles
    beamnodes = [BeamNode(float('inf'),
        [0] + sample(range(1,len(cities)),len(cities)-1),
        False) for i in range(best_n)
    ]
    while True:
        if all(bn.best for bn in beamnodes):
            break
        origin_len = len(beamnodes)
        for bn in beamnodes[:best_n]:
            if bn.best:
                continue
            swap_poses = combinations(range(1, len(cities)), 2)
            bn.best = True
            for p1, p2 in swap_poses:
                tp_path = bn.path[:]
                tp_path[p1],tp_path[p2]=tp_path[p2],tp_path[p1]
                tp_miles = calc_miles(tp_path)
                if bn.miles>tp_miles:
                    beamnodes.append(BeamNode(tp_miles, tp_path, False))
                    bn.best = False
        for i in range(origin_len-1, -1, -1):
            if beamnodes[i].best==False:
                del beamnodes[i]
        beamnodes.sort(key=lambda b:b.miles)
        for i in range(len(beamnodes)-1,0,-1):
            if beamnodes[i].path==beamnodes[i-1].path:
                del beamnodes[i]
        del beamnodes[best_n:]
    return beamnodes[0].miles, beamnodes[0].path

pre_running = float('inf')
for i in range(1,len(cities)):
    t1 = time.time()
    miles2, path2 = local_beam_search(cities[:i])
    t2 = time.time()
    print("running:{:g}s x{:.3f}, miles:{}".format(
        t2-t1, (t2-t1)/pre_running, miles2))
    pre_running = t2-t1
    
print(path2)
```
结果为：
```
running:7.29561e-05s x0.000, miles:inf
running:9.89437e-05s x1.356, miles:inf
running:8.86917e-05s x0.896, miles:20.120564493919822
running:0.000219822s x2.478, miles:39.42664606580856
running:0.000298023s x1.356, miles:67.77227033726385
running:0.000684023s x2.295, miles:83.7957265568911
running:0.00104809s x1.532, miles:84.38275768767652
running:0.00150609s x1.437, miles:84.85443680089674
running:0.00251985s x1.673, miles:92.40225788702355
running:0.0036428s x1.446, miles:110.34594071307717
running:0.00780702s x2.143, miles:110.43467064772815
running:0.00751591s x0.963, miles:110.6873206900153
running:0.0108171s x1.439, miles:112.50512934569768
running:0.0183601s x1.697, miles:121.9956838975673
running:0.022723s x1.238, miles:123.33870849212809
running:0.0264652s x1.165, miles:119.96267356436316
running:0.0486362s x1.838, miles:128.4628063591761
running:0.057281s x1.178, miles:122.9663639327066
running:0.0813909s x1.421, miles:124.25486091521238
running:0.065707s x0.807, miles:126.92932595500943
running:0.117161s x1.783, miles:133.13654831413592
running:0.0891521s x0.761, miles:150.30627405004412
running:0.155648s x1.746, miles:145.05216884825373
running:0.208064s x1.337, miles:143.00049220059273
running:0.278815s x1.340, miles:146.96940451967586
running:0.178803s x0.641, miles:164.6820308199761
running:0.287737s x1.609, miles:165.3623236641076
running:0.382443s x1.329, miles:158.89294601333305
running:0.492631s x1.288, miles:162.3182291669556
running:0.362232s x0.735, miles:170.8874003412022
running:0.474879s x1.311, miles:190.41522653837592
running:0.619453s x1.304, miles:183.8387589057086
running:0.524256s x0.846, miles:181.1192640226694
[0, 15, 18, 17, 11, 10, 9, 2, 19, 25, 20, 30, 3, 29, 31, 8, 26, 32, 24, 22, 21, 
23, 16, 12, 13, 28, 27, 14, 4, 5, 6, 7, 1]
```
局部束搜索的时间复杂度也非常低。和爬山法一样，并不能保证路径最优，下面来计算一下 100 次 24城市路径规划，使用束搜索比首优爬山法更好的比例：
```python
>>> sum(climb_TSP(cities)>=local_beam_search(cities) for i in range(100))
79
>>> sum(climb_TSP(cities[:15])>=local_beam_search(cities[:15]) for i in range(100))
87
>>> sum(climb_TSP(cities[:12])>=local_beam_search(cities[:12]) for i in range(100))
84
>>> sum(climb_TSP(cities[:8])>=local_beam_search(cities[:8]) for i in range(100))
98
```
可以看出，差不多有 80% 以上的概率是使用局部束搜索路径要小于等于首优爬山法。

### 遗传算法+局部束搜索解决TSP问题

在局部束搜索选出最优的若干个后继之后，再进行杂交，并选出其中的最优解决方案：
```python
def genetic_lbs(cities, best_n=5):
    class BeamNode:
        def __init__(self, miles, path, best):
            self.miles, self.path, self.best = miles, path, best
    def calc_miles(path):
        pre_pos = cities[path[-1]]
        tp_miles = 0
        for p in path:
            tp_miles += math.hypot(pre_pos[0]-cities[p][0],
                                  pre_pos[1]-cities[p][1])
            pre_pos = cities[p]
        return tp_miles
    beamnodes = [BeamNode(float('inf'),
        [0] + sample(range(1,len(cities)),len(cities)-1),
        False) for i in range(best_n)
    ]
    while True:
        if all(bn.best for bn in beamnodes):
            break
        origin_len = len(beamnodes)
        for bn in beamnodes[:best_n]:
            if bn.best:
                continue
            swap_poses = combinations(range(1, len(cities)), 2)
            bn.best = True
            for p1, p2 in swap_poses:
                tp_path = bn.path[:]
                tp_path[p1],tp_path[p2]=tp_path[p2],tp_path[p1]
                tp_miles = calc_miles(tp_path)
                if bn.miles>tp_miles:
                    beamnodes.append(BeamNode(tp_miles, tp_path, False))
                    bn.best = False
        for i in range(origin_len-1, -1, -1):
            if beamnodes[i].best==False:
                del beamnodes[i]
        beamnodes.sort(key=lambda b:b.miles)
        for i in range(len(beamnodes)-1,0,-1):
            if beamnodes[i].path==beamnodes[i-1].path:
                del beamnodes[i]
        # 添加遗传算法部分
        cur_len = len(beamnodes)
        for i in range(cur_len):
            if i >= best_n:break
            for j in range(i+1, cur_len):
                if i+j>=best_n:break
                for k in range(best_n-i-j):
                    # 基因交叉
                    cut_pos = int(random.uniform(0,1)*len(cities))
                    gene1 = beamnodes[i].path[:cut_pos]
                    for c in beamnodes[j].path:
                        if c not in gene1:
                            gene1.append(c)
                    gene2 = beamnodes[j].path[:cut_pos]
                    for c in beamnodes[i].path:
                        if c not in gene2:
                            gene2.append(c)
                    for gn in (gene1, gene2):
                        beamnodes.append(BeamNode(calc_miles(gn), gn, False))
        beamnodes.sort(key=lambda b:b.miles)
        del beamnodes[best_n:]
    return beamnodes[0].miles, beamnodes[0].path

pre_running = float('inf')
for i in range(1,len(cities)):
    t1 = time.time()
    miles2, path3 = genetic_lbs(cities[:i])
    t2 = time.time()
    print("running:{:g}s x{:.3f}, miles:{}".format(
        t2-t1, (t2-t1)/pre_running, miles2))
    pre_running = t2-t1
    
print(path3)
```
结果为：
```
running:9.5129e-05s x0.000, miles:inf
running:8.29697e-05s x0.872, miles:inf
running:0.000273943s x3.302, miles:20.120564493919822
running:0.000831842s x3.037, miles:39.42664606580856
running:0.000684977s x0.823, miles:67.77227033726385
running:0.000576019s x0.841, miles:83.7957265568911
running:0.000691891s x1.201, miles:84.38275768767652
running:0.00138283s x1.999, miles:84.85443680089674
running:0.00311399s x2.252, miles:101.54320632553853
running:0.00338936s x1.088, miles:110.34594071307717
running:0.00417089s x1.231, miles:110.43467064772815
running:0.00690198s x1.655, miles:110.6873206900153
running:0.00783372s x1.135, miles:118.61179311561754
running:0.0127091s x1.622, miles:125.54503467705224
running:0.0181692s x1.430, miles:115.47317097786926
running:0.0524771s x2.888, miles:127.99464841246441
running:0.0240021s x0.457, miles:135.89117182524507
running:0.0254512s x1.060, miles:143.12942423600904
running:0.0353081s x1.387, miles:127.72028423981939
running:0.045733s x1.295, miles:145.73380010213987
running:0.114953s x2.514, miles:127.16247007489218
running:0.0783792s x0.682, miles:135.87874295493916
running:0.0925252s x1.180, miles:154.53409753509217
running:0.10963s x1.185, miles:159.3709471133312
running:0.113485s x1.035, miles:142.36697765621736
running:0.189174s x1.667, miles:159.40344686112726
running:0.148898s x0.787, miles:167.4007401838031
running:0.254782s x1.711, miles:163.03971670099799
running:0.171009s x0.671, miles:172.40526500119958
running:0.332591s x1.945, miles:180.41696043718437
running:0.325376s x0.978, miles:168.22927920930735
running:0.352742s x1.084, miles:182.87656455613865
running:0.574277s x1.628, miles:180.18837936035655
[0, 1, 7, 14, 5, 4, 31, 30, 8, 26, 32, 24, 22, 23, 16, 15, 12, 13, 6, 27, 29, 3, 
28, 18, 19, 2, 25, 20, 21, 17, 11, 10, 9]
```

下面再测试一下解决方案的效果是否得到提高：
```python
>>> sum(local_beam_search(cities)>=genetic_lbs(cities) for i in range(100))
40
>>> sum(local_beam_search(cities[:12])>=genetic_lbs(cities[:12]) for i in range(100))
39
>>> sum(local_beam_search(cities[:15])>=genetic_lbs(cities[:15]) for i in range(100))
39
>>> sum(local_beam_search(cities[:8])>=genetic_lbs(cities[:8]) for i in range(100))
40
```
在这个实例中，添加了遗传算法之后，结果居然变差了。但是，并不是说遗传算法不行，而是遗传算法不适合这个问题。






