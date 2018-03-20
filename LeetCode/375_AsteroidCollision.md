---
export_on_save:
 html: true
---

# 735. Asteroid Collision

We are given an array `asteroids` of integers representing asteroids in a row.

For each asteroid, the absolute value represents its size, and the sign represents its direction (positive meaning right, negative meaning left). Each asteroid moves at the same speed.

Find out the state of the asteroids after all collisions. If two asteroids meet, the smaller one will explode. If both are the same size, both will explode. Two asteroids moving in the same direction will never meet.

>**Example 1:**
**Input:**
asteroids = [5, 10, -5]
**Output:** [5, 10]
**Explanation:**
The 10 and -5 collide resulting in 10.  The 5 and 10 never collide.


>**Example 2:**
**Input**:
asteroids = [8, -8]
**Output:** []
**Explanation:**
The 8 and -8 collide exploding each other.

>**Example 3:**
**Input:**
asteroids = [10, 2, -5]
**Output:** [10]
**Explanation:**
The 2 and -5 collide resulting in -5.  The 10 and -5 collide
 resulting in 10.

> **Example 4:**
**Input:**
asteroids = [-2, -1, 1, 2]
**Output:** [-2, -1, 1, 2]
**Explanation:**
The -2 and -1 are moving left, while the 1 and 2 are moving right.
Asteroids moving the same direction never meet, so no asteroids will meet each other.

**Note:**

- The length of `asteroids` will be at most `10000`.
- Each asteroid will be a non-zero integer in the range `[-1000, 1000]`.

---

My solution:

```python {.line-numbers}
class Solution:
    def asteroidCollision(self, asteroids):
        """
        :type asteroids: List[int]
        :rtype: List[int]
        """
        i = 0
        remains = []
        # 存放待 “撞击” 的正向移动的 asteroid
        cur_ps = []
        while i < len(asteroids):
            if asteroids[i] < 0:
                if len(cur_ps)==0:
                    remains.append(asteroids[i])
                    i += 1
                    continue
                else:
                    for j in range(len(cur_ps)-1, -1, -1):
                        if cur_ps[j]+asteroids[i] > 0:
                            # 负向移动的 asteroid 被撞毁
                            i += 1
                            break
                        elif cur_ps[j]+asteroids[i] < 0:
                            # 正向移动的 asteroid 被撞毁
                            del cur_ps[j]
                        else:
                            # 正负向移动的两个 asteroid 同时撞毁
                            del cur_ps[j]
                            i += 1
                            break
                continue
            cur_ps.append(asteroids[i])
            i += 1
        return remains + cur_ps
```
