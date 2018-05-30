---
export_on_save:
 html: true
---

@import "/blog_head.md"

# PCA 的简化数据

PCA 的原理见 [PCA的数学原理](https://zhuanlan.zhihu.com/p/21580949)。

PCA 的作用：用于数据预处理阶段，实现数据降维，剔除噪声。

## PCA 的思路

将数据转换成前 N 个主成分的伪代码大致如下：
1. 去除平均值
1. 计算协方差矩阵
1. 计算协方差矩阵的特征值和特征向量
1. 将特征向量从大到小排序
1. 保留最上面的 N 个特征向量
1. 将数据转换到上述 N 个特征向量构建的新空间中

## PCA 实现代码

```python
def pca(data_mat, top_nf):
    mean_vals = np.mean(data_mat, axis=0)
    std_data = data_mat - mean_vals
    # rowvar: 0-一行表示一个样本，not 0-一列表示一个样本
    cov_mat = np.cov(std_data, rowvar=0)
    eig_vals, eig_vects = np.linalg.eig(np.mat(cov_mat))
    eig_val_indices = np.argsort(eig_vals)
    n_eig_i = eig_val_indices[-1:-1-top_nf:-1]
    n_eig_vect = eig_vects[:, n_eig_i]
    low_dd_mat = std_data * n_eig_vect
    recon_mat = low_dd_mat*n_eig_vect.T+mean_vals
    return low_dd_mat, recon_mat
```

对 [PCA的数学原理](https://zhuanlan.zhihu.com/p/21580949) 中的例子进行测试：
```python
d_mat = np.mat([
    [-1, -2],
    [-1, 0],
    [0, 0],
    [2, 1],
    [0, 1]
])
ld, new_mat = pca(d_mat, 1)
plt.scatter(d_mat[:,0].flatten().A[0], 
            d_mat[:,1].flatten().A[0], 
            color="red", label="pre_data", alpha=.5)
plt.scatter(new_mat[:,0].flatten().A[0], 
            new_mat[:,1].flatten().A[0], 
            color="deepskyblue", label="post_data", alpha=.5)
plt.legend(loc="best", fontsize=15)
plt.show()
```
绘制的图像为：
![](/AdvancedAI/assets/20180530232259.png)

可以发现降维后的数据在一条直线上，其中`ld` 和 `new_mat` 的值为：
```python
>>> print("ld:\n", ld)
>>> print("new_mat", new_mat)
ld:
 [[-2.12132034]
 [-0.70710678]
 [ 0.        ]
 [ 2.12132034]
 [ 0.70710678]]
new_mat [[-1.5 -1.5]
 [-0.5 -0.5]
 [ 0.   0. ]
 [ 1.5  1.5]
 [ 0.5  0.5]]
```
可以直接使用低维的 `ld` 进行数据分析，这样数据降维的工作就完成了。

