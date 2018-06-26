---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 630. Course Schedule III

> <https://leetcode.com/problems/course-schedule-iii/description/>

## Description

There are `n` different online courses numbered from `1` to `n`. Each course has some duration(course length) t and closed on dth day. A course should be taken continuously for `t` days and must be finished before or on the d<sub>th</sub> day. You will start at the 1<sub>st</sub> day.

Given `n` online courses represented by pairs `(t,d)`, your task is to find the maximal number of courses that can be taken.

**Example**:
**Input**: `[[100, 200], [200, 1300], [1000, 1250], [2000, 3200]]`
**Output**: `3`
**Explanation**: There're totally 4 courses, but you can take 3 courses at most:
- First, take the 1st course, it costs 100 days so you will finish it on the 100th day, and ready to take the next course on the 101st day.
- Second, take the 3rd course, it costs 1000 days so you will finish it on the 1100th day, and ready to take the next course on the 1101st day. 
- Third, take the 2nd course, it costs 200 days so you will finish it on the 1300th day. 
The 4th course cannot be taken now, since you will finish it on the 3300th day, which exceeds the closed date.

**Note**:
1. The integer 1 <= d, t, n <= 10,000.
2. You can't take two courses simultaneously.

## Solution

### Solution-1

将课程表按课程用时排序，将课程逐个添加到一个数组中，并对该数组进行排序，并判断新数组是否满足条件，如果不满足，取消该课程的添加，之后继续进行下一个的操作：
```python {class=line-numbers}
class Solution:
    def scheduleCourse(self, courses):
        """
        :type courses: List[List[int]]
        :rtype: int
        """
        courses.sort()
        selected = []
        last_close = 0
        span_time = 0
        for i in courses:
            if max(last_close, i[1]) >= span_time+i[0]:
                tp_selected = selected[:]
                tp_selected.append(i)
                tp_selected.sort(key=lambda x:x[1])
                t = 0
                for j in tp_selected:
                    t += j[0]
                    if t > j[1]:
                        break
                else:
                    selected = tp_selected
                    span_time += i[0]
                    last_close = max(last_close, i[1])
        return len(selected)
```
结果：上述代码第 15 行的排序操作非常耗时，在 LeetCode 上发生时间超时错误；

### Solution-2

将课程表按课程用时排序，将每个课程按截止时间为排序值插入一个新数组，使用的插入方法为二分插入，以替代上面的代码的 添加+排序 两步操作，另外，判断是否满足条件只需要检查到插入点即可：
```python {class=line-numbers}
class Solution:
    def scheduleCourse(self, courses):
        """
        :type courses: List[List[int]]
        :rtype: int
        """
        import bisect
        courses.sort()
        selected = []
        last_close = 0
        span_time = 0
        for i in courses:
            use_time = span_time + i[0]
            if max(last_close, i[1]) >= use_time:
                tp_selected = selected[:]
                item = [i[1], i[0]]
                insert_p = bisect.bisect(selected, item)
                tp_selected.insert(insert_p, item)
                stop_pos = insert_p-1
                if stop_pos<0: stop_pos=None
                for j in tp_selected[:stop_pos:-1]:
                    if use_time > j[0]:
                        break
                    use_time -= j[1]
                else:
                    selected = tp_selected
                    last_close = tp_selected[-1][0]
                    span_time = span_time + i[0]
        return len(selected)
```
结果：上面代码通过测试，不过在 LeetCode 上的测试时间为 1400ms，还是太大。

注意：如果要倒叙遍历整个 `list a`，使用 `for i in a[:-1:-1]`，是错误的，切片的第二个 -1 表示最后一个元素，这会导致一个元素都不能得到。修改方法 `for i in a[::-1]` 或者是 `for i in a[:None:-1]`。

### Solution-3

前面都是优先选择时间短的课程，其实课程的截止时间更为重要，所以先按截止时间进行排序，然后一个个检测是否要选择：
```python {class=line-numbers}
class Solution:
    def scheduleCourse(self, courses):
        """
        :type courses: List[List[int]]
        :rtype: int
        """
        import heapq
        courses.sort(key=lambda x:x[1])
        record = []
        use_time = 0
        for i in courses:
            if use_time + i[0] <= i[1]:
                heapq.heappush(record, -i[0])
                use_time += i[0]
            else:
                if len(record) and i[0] < -record[0]:
                    pre_t = -heapq.heappop(record)
                    heapq.heappush(record, -i[0])
                    use_time -= pre_t-i[0]
        return len(record)
```
上面代码通过 LeetCode 测试，用时仅为 188ms，超过 100% 提交的代码。

**总结**：简单的代码通常用时也越少！
