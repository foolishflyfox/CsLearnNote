---
export_on_save:
 html: true
---

@import "/blog_head.md"

# SVD 简化数据

> 摘自《机器学习实践》

奇异值分解（SVD）的优缺点：
- 优点：简化数据，去除噪声，提高算法的结果
- 缺点：数据的转换可能难以理解
- 适用数据类型：数值型数据

利用 SVD ，我们能够用小得多的数据集来表示原始数据集。这样做实际上是去除了噪声和冗余信息。

## 隐性语义检索

最早的 SVD 应用之一就是信息检索，我们称利用 SVD 的方法为隐性语义检索（Latent Semantic Indexing, LSI）。在 LSI 中，一个矩阵是由文档和词语组成的。当我们在该矩阵上应用 SVD 时，就会构建出多个奇异值，这些奇异值代表了文档中的概念和主题，这一特点可以用于更高效的文档搜索。在词语拼写错误时，只基于词语存在与否的简单搜索方法会遇到困难。

## 奇异值和特征值的关系

奇异值是矩阵 $XX^T$ 特征值的平方根。    

## 利用 Python 实现 SVD

例如，我们需要对矩阵 $\begin{pmatrix} 1 & 1 \\ 7 & 7 \end{pmatrix}$ 进行奇异值分解：
```python
>>> import numpy as np
>>> u, sigma, v_t = np.linalg.svd([[1,1],[7,7]])
>>> u
array([[-0.14142136, -0.98994949],
       [-0.98994949,  0.14142136]])
>>> sigma
array([  1.00000000e+01,   2.82797782e-16])
>>> v_t
array([[-0.70710678, -0.70710678],
       [ 0.70710678, -0.70710678]])
```
对一个稍大的矩阵进行 SVD：
```python
data = [
    [1,1,1,0,0],
    [2,2,2,0,0],
    [1,1,1,0,0],
    [5,5,5,0,0],
    [1,1,0,2,2],
    [0,0,0,3,3],
    [0,0,0,1,1],
]
u, sigma, v_t = np.linalg.svd(data)
print(sigma)
```
结果为：
```
[  9.72140007e+00   5.29397912e+00   6.84226362e-01   1.25958571e-15
   2.05750739e-31]
```
只保留前 3 个奇异值，恢复原矩阵：
```python
sigma_mat = np.mat([[sigma[0],0,0],[0,sigma[1],0],[0,0,sigma[2]]])
np.set_printoptions(precision=2, suppress=True)
print(u[:, :3]*sigma_mat*v_t[:3,:])
```
结果为：
```
[[ 1.  1.  1.  0.  0.]
 [ 2.  2.  2.  0.  0.]
 [ 1.  1.  1. -0. -0.]
 [ 5.  5.  5.  0.  0.]
 [ 1.  1. -0.  2.  2.]
 [ 0.  0. -0.  3.  3.]
 [ 0.  0. -0.  1.  1.]]
```
确定要保留的奇异值的数目有很多启发式的策略，其中一个典型的做法是保留矩阵中  90% 的能量信息。为了计算总能量信息，我们将所有奇异值求其平方和，于是可以将奇异值的平方和累加到总值的 90% 为止。另一个启发式策略就是，当矩阵上有上万个奇异值时，就保留前面的2000或3000个。

尽管换一种方法不够优雅，但是在实际中更加容易实施。之所以说它不够优雅，因为在任何数据集上都不能保证前 3000 个奇异值就能包含 90% 的能量信息。

## 基于协同过滤的推荐引擎

协同过滤是通过将用户和其他用户的数据进行对比来实现推荐的。系统工具用户之间的相似度、事物之间的相似度进行推荐。在这种情况下，唯一要计算的数学方法就是相似度。

### 相似度计算

如有下面一个表：
||鳗鱼饭|日式炸鸡排|寿司饭|烤牛肉|手撕猪肉|
|-|-|-|-|-|-|
|Jim|2|0|0|4|4|
|John|5|5|5|3|3|
|Sally|2|4|2|1|2|

我们计算一下手撕猪肉和烤牛肉的相似度，用欧氏距离来计算：
$$\sqrt {(4-4)^2+(3-3)^2+(1-2)^2}=1$$
手撕猪肉和鳗鱼饭欧氏距离：
$$\sqrt {(2-4)^2+(5-3)^2+(2-2)^2}=2.83$$

相似度：$$similar = \frac1{1+euler\_dist}$$

第二种计算距离的方法是 **皮尔逊相关系数(Pearson correlation)**，它能够度量两个向量之间的相似程度，该方法相对于欧氏距离的一个优势在于，它对于用户评级的量级不敏感，比如某个狂躁这对所有物品的评分都是5分，而另一个忧郁者对所有物品的评分都是1分，皮尔逊系数会认为这两个向量是相等的。在 NumPy 中，皮尔逊相关系数的计算由函数 `corrcoef()` 进行，其范围为 -1~1，可以通过 `0.5+0.5*corrcoef()` 函数进行归一化。

第三种计算距离的方式是**余弦相似度(cosine similarity)**，其计算的是两个向量夹角的余弦值。如果夹角为 90°，则相似度为0，如果两个向量同方向，则相似度为1。其范围也是 -1~1，所以也要归一化。两个向量余弦相似度公式为：$$cos\theta=\frac{A \cdot B}{||A||\ ||B||}$$

3 种相似度计算写成函数为：
```python
import numpy as np

def euclidSim(in_a, in_b):
    return 1.0/(np.linalg.norm(in_a-in_b)+1)

def pearsSim(in_a, in_b):
    if len(in_a) < 3:
        return 1.0
    return 0.5+0.5*np.corrcoef(in_a, in_b, rowvar=False)[0][1]

def cosSim(in_a, in_b):
    num = float(in_a.T.dot(in_b))
    denom = np.linalg.norm(in_a)*np.linalg.norm(in_b)
    return 0.5+0.5*num/denom

data = np.mat([
    [1,1,1,0,0],
    [2,2,2,0,0],
    [1,1,1,0,0],
    [5,5,5,0,0],
    [1,1,0,2,2],
    [0,0,0,3,3],
    [0,0,0,1,1],
])

print("column 0 vs column 4:")
# 0列 和 4列 的相似度
print('euclidSim :', euclidSim(data[:, 0], data[:, 4]))
print('pearsSim :', pearsSim(data[:, 0], data[:, 4]))
print('cosSim :', cosSim(data[:, 0], data[:, 4]))

print()
print("column 0 vs column 0:")
# 0列 和 0列 的相似度
print('euclidSim :', euclidSim(data[:, 0], data[:, 0]))
print('pearsSim :', pearsSim(data[:, 0], data[:, 0]))
print('cosSim :', cosSim(data[:, 0], data[:, 0]))
```
结果为：
```
column 0 vs column 4:
euclidSim : 0.1336766024
pearsSim : 0.237686194076
cosSim : 0.547245559126

column 0 vs column 0:
euclidSim : 1.0
pearsSim : 1.0
cosSim : 1.0
```

### 基于物品的相似度还是基于用户的相似度？

使用基于物品的相似度还是基于用户的相似度，取决于用户或物品的数量。

如果用户的数据很多，那么我们可能倾向于基于物品相似度的计算方法。对于大多数产品导向的推荐引擎而言，用户的数量往往大于物品的数量，即购买商品的用户数会多于出售的商品种类。

### 推荐引擎的评价

如何对推荐引擎进行评价呢？此时我们既没有预测的目标值，也没有用户来调查他们对预测的满意程度。这里我们就可以采用交叉测试的方法。将某些已知的评分值去掉，然后对它们进行预测，最后计算预测值和真实值之间的差异。

通常，用于推荐引擎评价的指标是称为 **最小均方根误差（Root Mean Squared Error, RMSE）** 的指标。

## 示例：餐馆菜肴推荐引擎

推荐系统的工作过程是：给定一个用户，系统为该用户返回 N 个最好的推荐菜。为了实现这一点，则需要我们做到：
1. 寻找用户没有评级的菜肴，即在用户-物品矩阵中的0值
1. 在用户没有评级的所有物品中，对每个物品预计一个可能的评级分数
1. 对这些物品按评分从高到低进行排列，返回前 N 个物品

```python
def standEst(data_mat, user, simMeas, item):
    m, n = data_mat.shape
    if user >= m or item >= n:
        return 0
    score_sum = 0
    sim_sum = 0
    for i in range(n):
        if data_mat[user, i] == 0:
            continue
        # 选出两个物品中同一行都有评分的所有行
        overlap = np.nonzero(np.logical_and(data_mat[:, item],
                                           data_mat[:, i]))[0]
        if len(overlap)==0:
            sim = 0
        else:
            sim = simMeas(data_mat[overlap, item], data_mat[overlap, i])
        score_sum += data_mat[user, i] * sim
        sim_sum += sim
    return sim_sum and score_sum/sim_sum

def recommand(dataMat, user, N=3, simMeas=euclidSim, estMethod=standEst):
    item_scores = []
    for i in range(dataMat.shape[1]):
        if dataMat[user, i]!=0:
            continue
        cur_est = estMethod(dataMat, user, simMeas, i)
        item_scores.append((i, cur_est))
    return sorted(item_scores, key=lambda i:i[1], reverse=True)[:N]
    
# 每一行为一个用户的对各个菜品的评分
# 每一列为一个菜品的各个用户的评分
myMat = np.matrix([
    [4,4,0,2,2],
    [4,0,0,3,3],
    [4,0,0,1,1],
    [1,1,1,2,0],
    [2,2,2,0,0],
    [1,1,1,0,0],
    [5,5,5,0,0]
])

print(recommand(myMat, 2))
print(recommand(myMat, 2, simMeas=cosSim))
print(recommand(myMat, 2, simMeas=pearsSim))
```
结果为：
```
[(2, 3.0), (1, 2.8266504712098603)]
[(2, 2.5), (1, 2.0243290220056256)]
[(2, 2.5), (1, 2.0)]
```

## 用 SVD 实现图片分解与合成

设一个图片可以表示成 $m\times n$ 的矩阵，则可以将该矩阵进行如下分解：
$$M_{m\times n}=U_{m\times m}\Lambda_{m\times n}V^T_{n\times n}$$
若：$U_{m\times m}=(\bold u_1, \bold u_2, \cdots, \bold u_m)$，$V^T_{n\times n}=\begin{pmatrix}\bold v_1^T \\\bold v_2^T \\ \vdots \\ \bold v_n^T\end{pmatrix}$，$\Lambda_{m\times n}$ 对角线上的元素分别为 $(\lambda_1, \lambda_2,\cdots,\lambda_k)$，其中 $k=Rank(M)$，则 $M$ 可以表示成：
$$M = \lambda_1\bold u_1\bold v_1^T+\lambda_2\bold u_2\bold v_2^T+\cdots+\lambda_k\bold v_k^T$$

若 $$\frac{\sum_{i=1}^h\lambda_i}{\sum_{i=1}^k\lambda_i}>90\% $$则只需要保留 $U_{m\times m}$ 的前 h 列，$V_{n \times n}^T$ 的前 h 行，以及 $\Lambda_{m\times n}$ 的前 h 个数即可恢复原图片：

对下面的图片进行 SVD，并使用上述公式进行复原：
![](/AdvancedAI/assets/gray_dog.jpg)

```python
import PIL
import matplotlib
import matplotlib.pyplot as plt

im = PIL.Image.open("gray_dog.jpg")
im_mat = np.mat(im)
u, sigma, vt = np.linalg.svd(im_mat)


show_cnt = (0,1,5,10,30,50,100,150)
fig = plt.figure(figsize=(10, 5))
new_im_mat = np.mat(np.zeros_like(im_mat, dtype='f'))
for i in range(len(sigma)):
    new_im_mat += sigma[i]*u[:,i]*vt[i,:]
    if i in show_cnt:
        plt.subplot(2, 4,show_cnt.index(i)+1)
        plt.title(f"{i+1} features", fontsize=15)
        plt.imshow(new_im_mat, cmap="gray")
        plt.axis("off")
# plt.tight_layout()
plt.show()
```
效果为：
![](/AdvancedAI/assets/20180601231036.jpg)

