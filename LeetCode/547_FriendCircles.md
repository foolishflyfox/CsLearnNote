---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 547. Friend Circles

**Website** : https://leetcode.com/problems/friend-circles/

There are **N** students in a class. Some of them are friends, while some are not. Their friendship is transitive in nature. For example, if A is a **direct** friend of B, and B is a **direct** friend of C, then A is an **indirect** friend of C. And we defined a friend circle is a group of students who are direct or indirect friends.

Given a **N*N** matrix **M** representing the friend relationship between students in the class. If M[i][j] = 1, then the ith and jth students are direct friends with each other, otherwise not. And you have to output the total number of friend circles among all the students.

> **Example 1:**
**Input:** 
\[\[1,1,0],
 [1,1,0],
 [0,0,1]]
**Output:** 2
**Explanation:** The 0th and 1st students are direct friends, so they are in a friend circle. 
The 2nd student himself is in a friend circle. So return 2.

> **Example 2:**
**Input:** 
\[\[1,1,0],
 [1,1,1],
 [0,1,1]]
**Output:** 1
**Explanation:** The 0th and 1st students are direct friends, the 1st and 2nd students are direct friends, 
so the 0th and 2nd students are indirect friends. All of them are in the same friend circle, so return 1.

**Note:**
1. N is in range [1,200].
2. M[i][i] = 1 for all students.
3. If M[i][j] = 1, then M[j][i] = 1.

---

这个是判断某个图共有多少个连通块的问题，可以用静态数组解决实现低的空间复杂度，代码如下：
```python {.line-numbers}
class Solution:
    def findCircleNum(self, M):
        """
        :type M: List[List[int]]
        :rtype: int
        """
        self.M = M
        N = len(M)
        chain = [-1] * N
        self.chain = chain
        for i in range(1, N):
            for j in range(0, i):
                if M[i][j] == 1:
                    i_parent = self.find_parent(i)
                    j_parent = self.find_parent(j)
                    if i_parent != j_parent:
                        chain[j_parent] = i_parent
        return chain.count(-1)
                    
    def find_parent(self, v):
        parent = -1
        while v!=-1:
            parent = v
            v = self.chain[v]
        return parent
```

