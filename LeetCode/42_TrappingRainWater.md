---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 42. Trapping Rain Water

> <https://leetcode.com/problems/trapping-rain-water/description/>

## Description

Given *n* non-negative integers representing an elevation map where the width of each bar is 1, compute how much water it is able to trap after raining.

![](/LeetCode/rainwatertrap.png)
<sub>The above elevation map is represented by array [0,1,0,2,1,0,1,3,2,1,2,1]. In this case, 6 units of rain water (blue section) are being trapped. Thanks Marcos for contributing this image!</sub>

**Example**:
**Input**: `[0,1,0,2,1,0,1,3,2,1,2,1]`
**Output**: `6`

## Solution

### Solution-1

使用常规思路解决，看看就行，这思路我也是改了 6 遍才完成的，贴上来只是为了给各位看看使用 递归动态规划 比常规思路要简洁多少：
```python {class=line-numbers}
class Solution:
    def trap(self, height):
        """
        :type height: List[int]
        :rtype: int
        """
        if len(height)<3:return 0
        height.append(0)
        check_point = []
        ascend = True
        for i in range(1, len(height)):
            if ascend and height[i]<height[i-1]:
                ascend = False
                k = 0
                pre_max = None
                for j in range(len(check_point)-1, -1, -1):
                    if height[check_point[j]]<height[i-1]:
                        k += 1
                    else:
                        break
                    if pre_max==None or height[check_point[j]]>height[check_point[pre_max]]:
                        pre_max = j
                else:
                    if pre_max!=None:
                        del check_point[pre_max+1:]
                    check_point.append(i-1)
                    continue
                if k > 0:
                    del check_point[-k:]
                check_point.append(i-1)
                continue
            if height[i] > height[i-1]:
                ascend = True
        print(check_point)
        ret = 0
        for i in range(1, len(check_point)):
            left, right = check_point[i-1], check_point[i]
            level = min(height[left], height[right])
            for j in range(left+1, right):
                ret += max(level-height[j], 0)
        return ret
```
运行时间为 52 ms。

### Solution-2

使用递归动态规划，以最大的 `height` 为分割点，将原来的数组分成两半，分别计算，返回两半的结果之和。 每一半用类似的方法进行计算。

```python {class=line-numbers}
class Solution:
    def deal_mid(self, height):
        level = min(height[0], height[-1])
        return sum(max(0, level-i) for i in height)
    def deal_left(self, height):
        if len(height)<=2 or max(height[:-1])==0:return 0
        mid = height.index(max(height[:-1]))
        return self.deal_mid(height[mid:])+self.deal_left(height[:mid+1])
    def deal_right(self, height):
        if len(height)<=2 or max(height[1:])==0:return 0
        mid = height.index(max(height[1:]), 1)
        return self.deal_mid(height[:mid+1])+self.deal_right(height[mid:])
    def trap(self, height):
        """
        :type height: List[int]
        :rtype: int
        """
        if len(height)<3:return 0
        mid = height.index(max(height))
        return self.deal_left(height[:mid+1])+self.deal_right(height[mid:])
```
代码长度减少一半，运行时间为 48 ms，比之前的还要快！
