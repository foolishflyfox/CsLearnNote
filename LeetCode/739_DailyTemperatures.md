---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 739. Daily Temperatures

## Description

> <https://leetcode.com/problems/daily-temperatures/description/>

Given a list of daily `temperatures`, produce a list that, for each day in the input, tells you how many days you would have to wait until a warmer temperature. If there is no future day for which this is possible, put `0` instead.

For example, given the list `temperatures = [73, 74, 75, 71, 69, 72, 76, 73]`, your output should be `[1, 1, 4, 2, 1, 1, 0, 0]`.

Note: The length of `temperatures` will be in the range `[1, 30000]`. Each temperature will be an integer in the range `[30, 100]`.

## Solution

### Solution-1
``` python {class=line-numbers}
class Solution:
    def dailyTemperatures(self, temperatures):
        """
        :type temperatures: List[int]
        :rtype: List[int]
        """
        import bisect
        record = [[] for i in range(101)]
        for i in range(len(temperatures)):
            record[temperatures[i]].append(i)
        ret = [0]*len(temperatures)
        rank_days = []
        for days in record[:29:-1]:
            tp_days = []
            for day in days:
                pos = bisect.bisect(rank_days, day)
                if pos!=len(rank_days):
                    ret[day] = rank_days[pos]-day
                tp_days.append(day)
            for day in tp_days:
                bisect.insort(rank_days, day)
        return ret
```
LeetCode 测试结果 700 ms。

### Solution-2
```python {class=line-numbers}
class Solution:
    def dailyTemperatures(self, temperatures):
        """
        :type temperatures: List[int]
        :rtype: List[int]
        """
        import bisect
        record = [[] for i in range(101)]
        for i in range(len(temperatures)):
            record[temperatures[i]].append(i)
        ret = [0]*len(temperatures)
        rank_days = []
        for days in record[:29:-1]:
            if len(days)==0:continue
            pos = 0
            for day in days:
                pos = bisect.bisect(rank_days, day, pos)
                ret[day] = 0 if pos==len(rank_days) else rank_days[pos]-day
            rank_days = sorted(rank_days+days)
        return ret
```
LeetCode 测试结果：428 ms。

### Solution-3

使用栈的解决方案：

```python {class=line-numbers}
class Solution:
    def dailyTemperatures(self, temperatures):
        """
        :type temperatures: List[int]
        :rtype: List[int]
        """
        ret, stack = [0]*len(temperatures), []
        for d, t in enumerate(temperatures):
            while stack and t>temperatures[stack[-1]]:
                last_day = stack.pop()
                ret[last_day] = d - last_day
            stack.append(d)
        return ret
```

在求与 “最近的” 相关的问题，应该想到用栈解决。
