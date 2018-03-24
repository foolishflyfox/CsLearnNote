---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 491. Increasing Subsequences

Given an integer array, your task is to find all the different possible increasing subsequences of the given array, and the length of an increasing subsequence should be at least 2 .


**Example:**
> **Input:** [4, 6, 7, 7]
**Output:** 
\[[4, 6], [4, 7], [4, 6, 7], [4, 6, 7, 7], [6, 7], [6, 7, 7],[7,7], [4,7,7]]

**Note:**
- The length of the given array will not exceed 15.
- The range of integer in the given array is [-100,100].
- The given array may contain duplicates, and two equal integers should also be considered as a special case of increasing sequence.

---

My solution:

先创建两个变量，`storage` 用于存放产生的非递减 *list* 的 *list*（其中包含单元素的*list*，最后会去掉）。

主要思路是：从 `nums` 中取出元素，并与 `storage` 中的元素合并后重新放入到 `storage` 中，其中的关键是在重新合并后如何判定是否应该放入到 `storage` 中。

**其实很多算法的框架原理都比较简单，其中的选择策略不同就会产生很大的不同，最经典的是搜索和最优化时，后继节点的不同选择产生了一大堆的算法：BFS、DFS、深度受限优先搜索、迭代深入搜索等等。**

该算法基于规律：当检测到`storage`中的元素的最后一个元素与待组合的元素相同，并且与上一次的的组合个数不同时，就应该停止组合，例如：传入的数组为 `[4, 6, 7, 7]`，则根据下面的代码可得：
第一次迭代，当前元素7：
`storage=[[4]]`
第二次迭代，当前元素6：
`storage=[[4],[4,6]]`
`storage=[[4],[4,6],[6]]`
第三次迭代，当前元素7：
`storage=[[4],[4,6],[6],[6,7]]`
`storage=[[4],[4,6],[6],[6,7],[4,6,7]]`
`storage=[[4],[4,6],[6],[6,7],[4,6,7],[4,7]]`
`storage=[[4],[4,6],[6],[6,7],[4,6,7],[4,7],[7]]`
第四次迭代，当前元素7：
`storage=[[4],[4,6],[6],[6,7],[4,6,7],[4,7],[7],[7,7]]`
此时记录了最后一个元素与当前元素相同，原来共有1个7
`storage=[[4],[4,6],[6],[6,7],[4,6,7],[4,7],[7],[7,7],[4,7,7]]`
`storage=[[4],[4,6],[6],[6,7],[4,6,7],[4,7],[7],[7,7],[4,7,7],[4,6,7,7]]`
`storage=[[4],[4,6],[6],[6,7],[4,6,7],[4,7],[7],[7,7],[4,7,7],[4,6,7,7],[6,7,7]]`
之后要组合的元素是`[6]`，其中7的个数有原来的1变为0，所以停止组合；

最后删除所有单元素数组得到：
`[[4,6],[6,7],[4,6,7],[4,7],[7,7],[4,7,7],[4,6,7,7],[6,7,7]]`


```python {.line-numbers}
class Solution:
    def findSubsequences(self, nums):
        """
        :type nums: List[int]
        :rtype: List[List[int]]
        """
        storage = []
        used = []
        for v in nums:
            same_cnt = -1
            for i in range(len(storage)-1, -1, -1):
                if same_cnt==-1 and storage[i][-1] > v:
                    continue
                if same_cnt==-1 and storage[i][-1] < v:
                    storage.append(storage[i]+[v])
                    continue
                if storage[i][-1] == v:
                    if same_cnt == -1:
                        same_cnt = storage[i].count(v)
                        storage.append(storage[i]+[v])
                    else:
                        if storage[i].count(v)==same_cnt:
                            storage.append(storage[i]+[v])
                        else:
                            break
            if v not in used:
                used.append([v])
                storage.append([v])
        ret = []
        for a in storage:
            if len(a)!=1:
                ret.append(a)
        return ret
```

More elegent solution:
```python {.line-numbers}
class Solution:
    def findSubsequences(self, nums):
        """
        :type nums: List[int]
        :rtype: List[List[int]]
        """
        subs = {()}
        for num in nums:
            subs |= {sub + (num,)
                     for sub in subs
                     if not sub or sub[-1] <= num}
        return [sub for sub in subs if len(sub) >= 2]
```

## Analysis
该算法主要是思想和前面的思路完全相同，之所以程序能够这么短，是因为它使用了Python的语法糖：
- 使用 `set` 类型解决冲突问题，其中 `set` 中的元素必须是可散列的(hashable)，其中 `tuple` 类型是可散列的，而 `list` 是不可散列的；
```python {cmd}
a = {(1,),(2,3),(1,),(2,3)}
print(a)
```
- 两个 `set` 类型变量连接使用操作符 `|`，`tuple` 和 `list` 类型变量的连接使用操作符 `+`：
```python {cmd}
a = {(1,),(2,3)}
b = {(1,),(3,4)}
print(a | b)
print((1,2)+(2,3))
```

- 如果 `tuple` 或 `list` 实例的长度为0，则 `not` 该实例后返回True：
```python {cmd}
print(not [])
```

## 性能测试

使用我自己定义的函数进行 %timeit 测试，随机产生一个长度为50的`nums`，测试结果为：
```bash
7.78 ms ± 67 µs per loop (mean ± std. dev. of 7 runs, 100 loops each)
```
使用短代码的算法，测试结果为：
```bash
14.5 ms ± 244 µs per loop (mean ± std. dev. of 7 runs, 100 loops each)
```

可以看到，在50个元素时，时间少了一半，所以代码虽然长度较长，但是时间复杂度比下面的要高，说明力气没有白费 \^_\^ ，另外很多编程语言都没有Python类似的语法糖，所以第一个算法修改为其他语言也更加方便。

## 吐槽时间

最后吐槽一下 LeeteCode 的 Judge 系统，明明是正确的结果，却判我错误，随机测了100多组随机数都是正确的，后台能不能用心点 😔

