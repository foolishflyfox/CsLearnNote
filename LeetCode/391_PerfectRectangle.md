---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 391. Perfect Rectangle

> <https://leetcode.com/problems/perfect-rectangle/description/>

## Description

Given N axis-aligned rectangles where `N > 0`, determine if they all together form an exact cover of a rectangular region.

Each rectangle is represented as a bottom-left point and a top-right point. For example, a unit square is represented as `[1,1,2,2]`. (coordinate of bottom-left point is `(1, 1)` and top-right point is `(2, 2)`).

**Example 1:**
![](/LeetCode/rectangle_perfect.gif)
```
rectangles = [
  [1,1,3,3],
  [3,1,4,2],
  [3,2,4,4],
  [1,3,2,4],
  [2,3,3,4]
]
```
Return true. All 5 rectangles together form an exact cover of a rectangular region.


**Example 2:**
![](/LeetCode/rectangle_separated.gif)
```
rectangles = [
  [1,1,2,3],
  [1,3,2,4],
  [3,1,4,2],
  [3,2,4,4]
]
```
Return false. Because there is a gap between the two rectangular regions.

**Example 3:**
![](/LeetCode/rectangle_hole.gif)

```
rectangles = [
  [1,1,3,3],
  [3,1,4,2],
  [1,3,2,4],
  [3,2,4,4]
]
```
Return false. Because there is a gap in the top center.

**Example 4:**
![](/LeetCode/rectangle_intersect.gif)

```
rectangles = [
  [1,1,3,3],
  [3,1,4,2],
  [1,3,2,4],
  [2,2,4,4]
]
```
Return false. Because two of the rectangles overlap with each other.

## Solution

该题涉及到计算机图形学几何方面的知识。

```python {class=line-numbers}
class Solution:
    def isRectangleCover(self, rectangles):
        """
        :type rectangles: List[List[int]]
        :rtype: bool
        """
        if len(rectangles)==0:return False
        record = set()
        b_l = []
        t_r = []
        S = 0
        for i in range(0, len(rectangles)):
            b,l,t,r = rectangles[i]
            S += (r-l)*(t-b)
            tp_b_l = [[b_, l_] for b_, l_ in b_l if b_<b or l_<l]
            if len(tp_b_l)!=len(b_l) or len(b_l)==0:
                b_l = tp_b_l
                b_l.append([b, l])
            tp_t_r = [[t_, r_] for t_, r_ in t_r if t_>t or r_>r]
            if len(tp_t_r)!=len(t_r) or len(t_r)==0:
                t_r = tp_t_r
                t_r.append([t, r])
            for point in [(b,l),(b,r),(t,l),(t,r)]:
                record.remove(point) if point in record else record.add(point)
        if (len(b_l)>1 or len(t_r)>1 or len(record)!=4 or 
            (b_l[0][0], b_l[0][1])not in record or (t_r[0][0], t_r[0][1])not in record):
            return False
        return S==(t_r[0][0]-b_l[0][0])*(t_r[0][1]-b_l[0][1])
```

其中，record 用于记录每个矩阵的 4 个顶点，如果顶点在 record 中出现过就删除，如果没出现过就加入 （第 24 行代码逻辑），最后 record 中只能剩余 4 个顶点，否则就一定不是 perfect_rectangle。

第 26 行是为了阻止 `[[0,0,1,1],[0,0,2,1],[1,0,2,1],[0,2,2,3]]` 情况的错误。

另外写一下检测两个矩形是否重叠的检测：
```python
# 两个矩形是否重叠检测函数
def rectangle_overlap(rect1, rect2):
    left = max(rect1.left, rect2.left)
    bottom = max(rect1.bottom, rect2.bottom)
    right = min(rect1.right, rect2.right)
    top = min(rect1.top, rect2.top)
    return left<right and bottom < top
```
