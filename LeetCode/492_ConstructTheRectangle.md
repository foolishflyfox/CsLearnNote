---
export_on_save:
 html: true
---

@import "/blog_head.md"


# 492. Construct the Rectangle

> <https://leetcode.com/problems/construct-the-rectangle/description/>

## Description

For a web developer, it is very important to know how to design a web page's size. So, given a specific rectangular web page’s area, your job by now is to design a rectangular web page, whose length `L` and width `W` satisfy the following requirements:

1. The area of the rectangular web page you designed must equal to the given target area.
<br>
2. The width `W` should not be larger than the length `L`, which means `L >= W`.
<br>
3. The difference between length `L` and width `W` should be as small as possible.
You need to output the length `L` and the width `W` of the web page you designed in sequence.

**Example**:
>**Input**: 4
**Output**: [2, 2]
**Explanation**: The target area is 4, and all the possible ways to construct it are `[1,4], [2,2], [4,1]`. 
But according to requirement 2, [1,4] is illegal; according to requirement 3,  [4,1] is not optimal compared to [2,2]. So the length `L` is 2, and the width `W` is 2.

**Note**:
- The given area won't exceed 10,000,000 and is a positive integer
- The web page's width and length you designed must be positive integers.

## Solution

版本1：
```python
class Solution:
    def constructRectangle(self, area):
        """
        :type area: int
        :rtype: List[int]
        """
        import math
        L = math.ceil(area**.5)
        while area%L:
            L += 1
        return L,area//L
```
该算法在 LeetCode 上测试的结果为 1596 ms。

非常耗时，因为在以 $k=\sqrt{area}$ 为分界点，前后能够整除 $area$ 的因子个数是相同的，设为 $n$ 个，而前面的因子的密度为 $n/k$，而后面的因子的密度约为 $n/(k^2-k)$，而 $k << k^2-k$，当 $area$ 较大时，搜寻 $k$ 之前的部分效率远比搜寻 $k$ 之后的部分要高。

版本2：
```python
class Solution:
    def constructRectangle(self, area):
        """
        :type area: int
        :rtype: List[int]
        """
        W = int(area**.5)
        while area%W:
            W -= 1
        return [area//W,W]
```
该算法在 LeetCode 上测试的结果为 60 ms，与上一版本相比快了很多。

版本3：
```python
class Solution:
    def constructRectangle(self, area):
        """
        :type area: int
        :rtype: List[int]
        """
        import math
        W = math.floor(area**.5)
        while area%W:
            W -= 1
        return [area//W,W]
```
该算法在 LeetCode 上测试的结果为 40 ms，可见使用 math 的 `floor()` 运算比 `int()` 要高效很多。


