---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 729. My Calendar I

> <https://leetcode.com/problems/my-calendar-i/description/>

## Description

Implement a `MyCalendar` class to store your events. A new event can be added if adding the event will not cause a double booking.

Your class will have the method, `book(int start, int end)`. Formally, this represents a booking on the half open interval `[start, end)`, the range of real numbers `x` such that `start <= x < end`.

A double booking happens when two events have some non-empty intersection (ie., there is some time that is common to both events.)

For each call to the method `MyCalendar.book`, return `true` if the event can be added to the calendar successfully without causing a double booking. Otherwise, return `false` and do not add the event to the calendar.

Your class will be called like this: 
`MyCalendar cal = new MyCalendar(); MyCalendar.book(start, end)`

**Example 1**:
```
MyCalendar();
MyCalendar.book(10, 20); // returns true
MyCalendar.book(15, 25); // returns false
MyCalendar.book(20, 30); // returns true
```
**Explanation**: 
- The first event can be booked.  The second can't because time 15 is already booked by another event.
- The third event can be booked, as the first event takes every time less than 20, but not including 20.

**Note**:
The number of calls to `MyCalendar.book` per test case will be at most `1000`.
In calls to `MyCalendar.book(start, end)`, `start` and `end` are integers in the range `[0, 10^9]`.

## Solution

### Solution-1

二叉树的方式递归解决：
```python {class=line-numbers}
from collections import namedtuple
Event = namedtuple('Event', 'start end')
class Node:
    def __init__(self, event):
        self.value = event
        self.left = None
        self.right = None
class MyCalendar(object):
    def __init__(self):
        self.tree = None
    def is_confilict(self, e1, e2):
        if e1.end <= e2.start or e2.end<=e1.start:
            return False
        return True
    def add_event(self, root, e):
        if self.is_confilict(root.value, e):
            return False
        else:
            if e.end <= root.value.start:
                if root.left == None:
                    root.left = Node(e)
                    return True
                else:
                    return self.add_event(root.left, e)
            else:
                if root.right == None:
                    root.right = Node(e)
                    return True
                else:
                    return self.add_event(root.right, e)
    def book(self, start, end):
        """
        :type start: int
        :type end: int
        :rtype: bool
        """
        if self.tree==None:
            self.tree = Node(Event(start, end))
            return True
        return self.add_event(self.tree, Event(start, end))
```

LeetCode 测试运行：1063 ms。

### Solution-2

用二分查找+list的方式代替树结构：
```python {class=line-numbers}
import bisect
class MyCalendar(object):
    def __init__(self):
        self.events = []
    def book(self, start, end):
        """
        :type start: int
        :type end: int
        :rtype: bool
        """
        p = bisect.bisect_left(self.events, (start, end))
        for i in (0, 1, -1):
            t = p+i
            if 0<=t<len(self.events) and not(
            self.events[t][0]>=end or self.events[t][1]<=start):
                return False
        self.events.insert(p, (start, end))
        return True
```

LeetCode 测试运行：	257 ms。
