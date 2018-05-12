---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 466. Count The Repetitions

> <https://leetcode.com/problems/count-the-repetitions/description/>

## Description

Define `S = [s,n]` as the string S which consists of n connected strings s. For example, `["abc", 3] ="abcabcabc"`.

On the other hand, we define that string s1 can be obtained from string s2 if we can remove some characters from s2 such that it becomes s1. For example, “abc” can be obtained from “abdbec” based on our definition, but it can not be obtained from “acbbe”.

You are given two non-empty strings s1 and s2 (each at most 100 characters long) and two integers 0 ≤ n1 ≤ 106 and 1 ≤ n2 ≤ 106. Now consider the strings S1 and S2, where `S1=[s1,n1]` and `S2=[s2,n2]`. Find the maximum integer M such that `[S2,M]` can be obtained from `S1`.

**Example**:

>Input:
s1="acb", n1=4
s2="ab", n2=2
Return:
2

## Solutions

### Solution-1

```python
class Solution:
    def getMaxRepetitions(self, s1, n1, s2, n2):
        """
        :type s1: str
        :type n1: int
        :type s2: str
        :type n2: int
        :rtype: int
        """
        if not set(s2)<=set(s1):
            return 0
        def S1_generator(s1, n1):
            for i in range(n1):
                for c in s1:
                    yield c
        cnt = 0
        p = 0
        for c in S1_generator(s1, n1):
            if c==s2[p]:
                p += 1
                if p==len(s2):
                    p = 0
                    cnt += 1
        return cnt//n2
```
优点：算法简单
缺点：时间复杂度为$O(n*len(s1))$

### Solution-2

The main idea is to find a circle. A circle means m rounds of s1 has a subarray of n rounds of s2, and at the meantime starting from the same point of s1.

```python
class Solution(object):
    def getMaxRepetitions(self, s1, n1, s2, n2):
        start = {} # s2_idx : s1_round, s2_round
        s1_round, s2_round, s2_idx = 0, 0, 0
        while s1_round < n1:
            s1_round += 1
            for ch in s1:
                if ch == s2[s2_idx]:
                    s2_idx += 1
                    if s2_idx == len(s2):
                        s2_round += 1
                        s2_idx = 0
            if s2_idx in start:
                prev_s1_round, prev_s2_round = start[s2_idx]
                circle_s1_round = s1_round - prev_s1_round
                circle_s2_round = s2_round - prev_s2_round
                res = ((n1 - prev_s1_round) / circle_s1_round 
                    * circle_s2_round)
                left_s1_round = ((n1 - prev_s1_round) 
                    % circle_s1_round + prev_s1_round)

                for key, val in start.iteritems():
                    if val[0] == left_s1_round:
                        res += val[1]
                        break
                return res / n2
            else:
                start[s2_idx] = (s1_round, s2_round)
        return s2_round / n2
```
