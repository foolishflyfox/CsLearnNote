---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 793. Preimage Size of Factorial Zeroes Function

Let `f(x)` be the number of zeroes at the end of `x!`. (Recall that `x! = 1 * 2 * 3 * ... * x`, and by convention, `0! = 1`.)

For example, `f(3) = 0` because 3! = 6 has no zeroes at the end, while `f(11) = 2` because 11! = 39916800 has 2 zeroes at the end. Given `K`, find how many non-negative integers `x `have the property that `f(x) = K`.

>**Example 1:**
**Input:** K = 0
**Output:** 5
**Explanation:** 0!, 1!, 2!, 3!, and 4! end with K = 0 zeroes.

>**Example 2:**
**Input:** K = 5
**Output:** 0
**Explanation:** There is no x such that x! ends in K = 5 zeroes.

**Note:**
- `K` will be an integer in the range `[0, 10^9]`.

---

如果存在`f(x)`使得`f(x)=K`,如`f(0)=0`，则必有`f(1) = f(2) = f(3) = f(4) = 0`，因为连续的5个数中，必定只有一个是5的倍数，而确定`x!`结果最后0的个数，只需要计算其中因子5的个数即可。因此输出的结果只有两种，0或者5。

该题目原来的思路得到一个数组`[0, 1, 2, 3, 4, 6, 7, 8, 9, 10, 12, 13, ...]`，其中的每个元素都是可能的`K`值，根据得到的数组总结出一个关于`K`的规律，然后根据该规律计算任意给定的`K`值是否在该数组中，如果在该数组中返回 5，否则返回 0。

其中找规律就非常麻烦，这已经是一个数学问题了。需要有元素个数足够的数组，这样才能找到正确的规律，如果数组中的元素错误，那么根本不可能找到其中的规律或者是找到错误的规律；并且中间过程中有点错误就可能导致规律不正确。

上面的思路类似于人工智能前期的专家系统，通过人工找到一定的规律（“知识”）并创建一个智能体，将人工找到的“知识”输入到智能体中。而这样一个小题目找规律就比较麻烦了，更何况是复杂的系统呢。因此，专家系统的模型构建非常困难也是情有可原的。

另一种思路是寻找一个`n`，使得`f(n)=K`，如果能找到，就返回5，找不到就返回0。如果取`n`从0开始，而`K`的值如果是非常大，可以想象如果n每次都加1，直到`f(n)>=K`结束，这一过程的时间复杂度非常高，所以刚开始我就把这种思路给否定了。不过，如果能够将寻找的效率提高（如使用二分查找），而且就算`f(n)`时并不一定要计算出`n!`，通过算法加快`f(n)`的计算，那么时间复杂度能够大大降低。所以有了下面的代码：

Solution:

```python
class Solution:
    def preimageSizeFZF(self, K):
        """
        :type K: int
        :rtype: int
        """
        # 该函数判断 n! 后面有多少个0，不用计算 n!
        def nzero(n):
            cnt = 0
            x = 5
            while x <= n:
                cnt += n//x
                x *= 5
            return cnt
        # f(0) <= K <= f(5*K)
        # 用二分查找在 0 ~ 5*K 之间的一个值n，使得
        # f(n) = K，如果找到就可返回5，否则返回0
        min_v = 0
        max_v = 5*K
        while min_v <= max_v:
            mid_v = (min_v+max_v)//2
            mid_zeros = nzero(mid_v)
            if mid_zeros < K:
                min_v = mid_v + 1
            elif mid_zeros > K:
                max_v = mid_v - 1
            else:
                return 5
        return 0
```

## 总结

计算机擅长枚举，只要我们缩小可能的取值空间，选择合适的查找算法，就能够将一些复杂的搜索/优化问题变得简单。

另外，不要把题目变成数学问题，将规律总结出来的确能够大大减小算法的时间复杂度，甚至可能是降到 $O(1)$，但这种方式通常非常费时。
