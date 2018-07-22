---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 621. Task Scheduler

> <https://leetcode.com/problems/task-scheduler/description/>

## Description

Given a char array representing tasks CPU need to do. It contains capital letters A to Z where different letters represent different tasks.Tasks could be done without original order. Each task could be done in one interval. For each interval, CPU could finish one task or just be idle.

However, there is a non-negative cooling interval n that means between two **same tasks**, there must be at least n intervals that CPU are doing different tasks or just be idle.

You need to return the **least** number of intervals the CPU will take to finish all the given tasks.

**Example 1**:
**Input**: `tasks = ["A","A","A","B","B","B"], n = 2`
**Output**: `8`
**Explanation**: `A -> B -> idle -> A -> B -> idle -> A -> B`.
**Note**:
- The number of tasks is in the range [1, 10000].
- The integer n is in the range [0, 100].

## Solution
```python
class Solution(object):
    def leastInterval(self, tasks, n):
        """
        :type tasks: List[str]
        :type n: int
        :rtype: int
        """
        from collections import deque
        record = [0]*26
        for c in tasks: record[ord(c)-65] += 1
        tasks_que = deque([(i, 1) for i in range(26) if record[i]])
        ret = 0
        while tasks_que:
            ret += 1
            if tasks_que[0][1]<=ret:
                task_id, task_time = tasks_que.popleft()
                record[task_id] -= 1
                if record[task_id]>0:
                    tasks_que.append((task_id, task_time+n+1))
        return ret
```
