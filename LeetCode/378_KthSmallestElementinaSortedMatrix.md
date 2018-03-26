---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 378. Kth Smallest Element in a Sorted Matrix
> <https://leetcode.com/problems/kth-smallest-element-in-a-sorted-matrix/>
> 该题涉及的算法是二分查找寻找数据插入数组的位置

Given a n x n matrix where each of the rows and columns are sorted in ascending order, find the kth smallest element in the matrix.

Note that it is the kth smallest element in the sorted order, not the kth distinct element.

**Example:**
```
matrix = [
   [ 1,  5,  9],
   [10, 11, 13],
   [12, 13, 15]
],
k = 8,

return 13.
```
**Note:**
You may assume k is always valid, 1 ≤ k ≤ n2.

---

一个非常简单的解决方法 :
```python {.line-numbers}
class Solution:
    def kthSmallest(self, matrix, k):
        """
        :type matrix: List[List[int]]
        :type k: int
        :rtype: int
        """
        merge_a = []
        for a in matrix:
            merge_a += a
        merge_a.sort()
        return merge_a[k-1]
```
只需要 5 条语句就可以实现功能，但是没有把条件：*每一行都是有序的* 利用起来，存在一定的性能缺陷，使用一个100×100的`matrix`，k为300时，其性能测试为：
```
1.19ms ± 9.38µs per loop (mean ± std. dev. of 7 runs, 1000 loops each)
```

下面我们把给定的条件用起来，基本思路是：先对 `matrix` 按第一个元素进行排序 (n个元素的排序)，之后，循环过程：从`matrix`中取出第一行的数组，将该数组第一个元素删除，如果该数组不为空，利用二分查找搜索要插入的位置，并将该数组重新插回`matrix`。直到删除了`k-1`个元素，循环结束后，返回`matrix[0][0]`。

## 寻找插入位置

我们先来写一个搜索插入位置的函数：
```python {cmd id="sip"}
def search_insert_pos(a, v, ascend=True, compf=None):
    """
    :type a: list, an ordered list
    :type v: number, want insert value
    :type ascend: bool, the order style
    :type compf: define compare function by user
    :rtype : the insert pos
    """
    left_pos, right_pos = 0, len(a)-1
    if compf==None:
        if ascend:
            compare_fun = lambda x,y:x>=y
        else:
            compare_fun = lambda x,y:x<=y
    else:
        compare_fun = compf
    # 以 a 为非递减数组为例,while循环后left_pos
    # 是 a.index(min(a中不小于v的值))
    while left_pos<=right_pos:
        mid_pos = (left_pos+right_pos)//2
        if compare_fun(a[mid_pos], v):
            right_pos = mid_pos - 1
        else:
            left_pos = mid_pos + 1
    return left_pos
```
下面介绍该函数的使用：

### 用法示例

### 插入排序 - 递增
```python {cmd continue="sip"}
a = [1,5,4,0,7,8,1,6]
b = []
for i in a:
    b.insert(search_insert_pos(b, i), i)
print(b)
```
上述算法的时间复杂度为：$O(n\log n)$

### 插入排序 - 递减
```python {cmd continue="sip"}
a = [1,5,4,0,7,8,1,6]
b = []
for i in a:
    b.insert(search_insert_pos(b, i, False), i)
print(b)
```

### 自定义排序类型
例如，该题目中需要按矩阵中`list`元素的第一个元素值进行排序：
```python {cmd continue="sip"}
a = [[1,2,5],[5,9],[3,9],[0,8,2],[1,6],[1,0]]
b = []
for i in a:
    b.insert(search_insert_pos(b, i, 
        compf=lambda x,y:x[0]>=y[0]),i)
print(b)
```

## 一个改进版本

>**注意：我们对matrix的每个元素都进行了`copy()`，而不仅仅是 `matrix.copy()`，因为使用后一种方式复制的是`matrix`中数组的索引，而不是其内容，修改后会对`matrix`产生影响。**

如：
```python {cmd}
matrix = [[1,2],[3,4]]
m_cp = matrix.copy()
m_cp[0][0] = 10
print(matrix)
```

改进后的版本为：
```python {.line-numbers}
class Solution:
    def kthSmallest(self, matrix, k):
        """
        :type matrix: List[List[int]]
        :type k: int
        :rtype: int
        """
        m_cp = [tmp.copy() for tmp in matrix]
        m_cp.sort()
        for i in range(k-1):
            del m_cp[0][0]
            tmp_a = m_cp[0]
            del m_cp[0]
            if len(tmp_a)==0:
                continue
            else:
                # 二分查找寻找位置
                m_cp.insert(search_insert_pos(m_cp,tmp_a,
                    lambda x,y:x[0]>=y[0]),tmp_a)
        return m_cp[0][0]                
```

其测试性能为：
```
786µs ± 6.43µs per loop (mean ± std. dev. of 7 runs, 1000 loops each)
```
可以看出比最先的版本的确效率要高一些。

## 改进的嵌入版本

将二分搜索插入位置的算法代码嵌入到`kthSmallest`内部：
```python
class Solution:
    def kthSmallest(self, matrix, k):
        """
        :type matrix: List[List[int]]
        :type k: int
        :rtype: int
        """
        m_cp = [tmp.copy() for tmp in matrix]
        m_cp.sort()
        for i in range(k-1):
            del m_cp[0][0]
            tmp_a = m_cp[0]
            del m_cp[0]
            if len(tmp_a)==0:
                continue
            else:
                # 二分查找寻找位置
                min_i, max_i = 0, len(m_cp)-1
                while(min_i <= max_i):
                    mid_i = (min_i+max_i)//2
                    if m_cp[mid_i][0] >= tmp_a[0]:
                        max_i = mid_i -1
                    else:
                        min_i = mid_i + 1
                m_cp.insert(min_i, tmp_a)
        return m_cp[0][0]
```
其性能测试为：
```
553µs ± 6.9µs per loop (mean ± std. dev. of 7 runs, 1000 loops each)
```
与上一版本相比没有了函数外部调用，性能又有了较大提升，比最先的版本快一倍。

