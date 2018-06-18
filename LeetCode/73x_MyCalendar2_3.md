---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 731/732 My Calendar

该博文包含相关联的两道题：
- [My Calendar II](https://leetcode.com/problems/my-calendar-ii/description/)
- [My Calendar III](https://leetcode.com/problems/my-calendar-iii/description/)

## My Calendar II

### Description

Implement a `MyCalendarTwo `class to store your events. A new event can be added if adding the event will not cause a triple booking.

Your class will have one method, `book(int start, int end)`. Formally, this represents a booking on the half open interval `[start, end)`, the range of real numbers `x` such that `start <= x < end`.

A triple booking happens when **three** events have some non-empty intersection (ie., there is some time that is common to all 3 events.)

For each call to the method `MyCalendar.book`, return `true` if the event can be added to the calendar successfully without causing a **triple** booking. Otherwise, return `false` and do not add the event to the calendar.

Your class will be called like this: `MyCalendar cal = new MyCalendar(); MyCalendar.book(start, end)`

**Example 1**:
```
MyCalendar();
MyCalendar.book(10, 20); // returns true
MyCalendar.book(50, 60); // returns true
MyCalendar.book(10, 40); // returns true
MyCalendar.book(5, 15); // returns false
MyCalendar.book(5, 10); // returns true
MyCalendar.book(25, 55); // returns true
```
**Explanation**: 
- The first two events can be booked.  The third event can be double booked.
- The fourth event (5, 15) can't be booked, because it would result in a triple booking.
- The fifth event (5, 10) can be booked, as it does not use time 10 which is already double booked.
- The sixth event (25, 55) can be booked, as the time in [25, 40) will be double booked with the third event;
- the time [40, 50) will be single booked, and the time [50, 55) will be double booked with the second event.

**Note**:

- The number of calls to `MyCalendar.book` per test case will be at most `1000`.
In calls to `MyCalendar.book(start, end),` `start` and `end` are integers in the range `[0, 10^9]`.

### Solution

和 MyCalendI 题目类似，我们产生一个数组用于记录已经重合的区间`overlap`，每次比较新 book 的区间与重合区间是否有重合：
```python {class=line-numbers}
class MyCalendarTwo(object):
    def __init__(self):
        self.calendar = []
        self.overlap = []

    def book(self, start, end):
        """
        :type start: int
        :type end: int
        :rtype: bool
        """
        for (o_start, o_end) in self.overlap:
            if start < o_end and end > o_start:
                return False
        for (c_start, c_end) in self.calendar:
            if start < c_end and end > c_start:
                self.overlap.append((max(start,c_start),min(end, c_end)))
        self.calendar.append((start, end))
        return True
```
## My Calendar III

### Description

Implement a `MyCalendarThree` class to store your events. A new event can always be added.

Your class will have one method, `book(int start, int end)`. Formally, this represents a booking on the half open interval `[start, end)`, the range of real numbers `x` such that `start <= x < end`.

A K-booking happens when **K** events have some non-empty intersection (ie., there is some time that is common to all K events.)

For each call to the method `MyCalendar.book`, return an integer `K` representing the largest integer such that there exists a `K-booking` in the calendar.

Your class will be called like this: `MyCalendarThree cal = new MyCalendarThree()`; `MyCalendarThree.book(start, end)`

**Example 1**:
```
MyCalendarThree();
MyCalendarThree.book(10, 20); // returns 1
MyCalendarThree.book(50, 60); // returns 1
MyCalendarThree.book(10, 40); // returns 2
MyCalendarThree.book(5, 15); // returns 3
MyCalendarThree.book(5, 10); // returns 3
MyCalendarThree.book(25, 55); // returns 3
```

**Explanation**: 
- The first two events can be booked and are disjoint, so the maximum K-booking is a 1-booking.
- The third event [10, 40) intersects the first event, and the maximum K-booking is a 2-booking.
- The remaining events cause the maximum K-booking to be only a 3-booking.
- Note that the last event locally causes a 2-booking, but the answer is still 3 because : eg. `[10, 20)`, `[10, 40)`, and `[5, 15)` are still triple booked.

**Note**:
- The number of calls to `MyCalendarThree.book` per test case will be at most `400`.
- In calls to `MyCalendarThree.book(start, end`), `start` and `end` are integers in the range `[0, 10^9]`.

### Solution

延续上面的思路，通过多层数组记录重叠区域：
```python
class MyCalendarThree(object):
    def __init__(self):
        self.overlaps = []
        
    def book(self, start, end):
        """
        :type start: int
        :type end: int
        :rtype: bool
        """
        if len(self.overlaps)==0:
            self.overlaps.append(set())
            self.overlaps[0].add((start, end))
            return 1
        for i in range(len(self.overlaps)-1,-1,-1):
            tmp_laps = self.overlaps[i]
            for rg in tmp_laps:
                if not(rg[0]>=end or rg[1]<=start):
                    if i==len(self.overlaps)-1:
                        self.overlaps.append(set())
                    self.overlaps[i+1].add((max(start, rg[0]),
                                           min(end, rg[1])))
        self.overlaps[0].add((start, end))
        return len(self.overlaps)
```

不过会发生 Time Limit Error，提交时超时，解决方法：合并重叠的区间，以减少遍历所消耗的时间；

```python
class MyCalendarThree(object):
    def __init__(self):
        self.overlaps = []
    # 合并重叠区间
    def merge_range(self, events, start, end):
        new_events = []
        for rg in events:
            if rg[0]<end and rg[1]>start:
                start = min(rg[0], start)
                end = max(rg[1], end)
            else:
                new_events.append(rg)
        new_events.append((start, end))
        return new_events
    def book(self, start, end):
        """
        :type start: int
        :type end: int
        :rtype: bool
        """
        if len(self.overlaps)==0:
            self.overlaps.append(list())
            self.overlaps[0].append((start, end))
            return 1
        for i in range(len(self.overlaps)-1,-1,-1):
            tmp_laps = self.overlaps[i]
            for rg in tmp_laps:
                if rg[0]<end and rg[1]>start:
                    if i==len(self.overlaps)-1:
                        self.overlaps.append(list())
                    self.overlaps[i+1]= self.merge_range(self.overlaps[i+1],
                                            max(start, rg[0]),min(end, rg[1]))
        self.overlaps[0] = self.merge_range(self.overlaps[0], start, end)
        return len(self.overlaps)
```
该代码通过测试；

使用二分查找进一步加速：
```python
import bisect
class MyCalendarThree(object):
    def __init__(self):
        self.overlaps = []
    def merge_range(self, events, start, end):
        new_events = []
        lp = rp = bisect.bisect_left(events, (start, end))
        tp_start, tp_end = start, end
        while (0<=lp<len(events) and events[lp][0]<end 
               and events[lp][1]>start):
            tp_start = min(tp_start, events[lp][0])
            tp_end = max(tp_end, events[lp][1])
            lp -= 1
        lp += 1
        while (0<=rp<len(events) and events[rp][0]<end 
               and events[rp][1]>start):
            tp_start = min(tp_start, events[lp][0])
            tp_end = max(tp_end, events[lp][1])
            rp += 1
        return events[:lp]+[(tp_start, tp_end)]+events[rp:]
    def book(self, start, end):
        """
        :type start: int
        :type end: int
        :rtype: bool
        """
        if len(self.overlaps)==0:
            self.overlaps.append(list())
            self.overlaps[0].append((start, end))
            return 1
        for i in range(len(self.overlaps)-1,-1,-1):
            tmp_laps = self.overlaps[i]
            for rg in tmp_laps:
                if not(rg[0]>=end or rg[1]<=start):
                    if i==len(self.overlaps)-1:
                        self.overlaps.append(list())
                    self.overlaps[i+1]= self.merge_range(self.overlaps[i+1],
                                            max(start, rg[0]),min(end, rg[1]))
        self.overlaps[0] = self.merge_range(self.overlaps[0], start, end)
        return len(self.overlaps)
```
该代码结果正确，不过性能不升反降。总结原因是：数量还是太小，维护一个二分查找的数和进行二分查找使用的时间还大于遍历算法。

### 总结

- 无论什么算法，没有绝对的好坏，只是适不适合某个问题。有的算法如二分查找对大数据量非常有效，但小数据时可能还不如简单的遍历/枚举；
<br>
- 计算机逻辑思想能够解决任务就不能求解的问题（如用图灵机模型解决哥德尔不完备定理证明）；而数学能够在得到确定的回答后，通过定理加速求解过程。
