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

Analysis:

