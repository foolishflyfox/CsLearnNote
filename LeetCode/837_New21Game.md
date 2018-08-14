---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 837. New 21 Game

> <https://leetcode.com/problems/new-21-game/description/>

## Description

Alice plays the following game, loosely based on the card game "21".

Alice starts with `0` points, and draws numbers while she has less than `K` points.  During each draw, she gains an integer number of points randomly from the range `[1, W]`, where `W` is an integer.  Each draw is independent and the outcomes have equal probabilities.

Alice stops drawing numbers when she gets `K` or more points.  What is the probability that she has `N` or less points?


**Example 1**:
**Input**: `N = 10, K = 1, W = 10`
**Output**: `1.00000`
**Explanation**:  Alice gets a single card, then stops.

**Example 2**:
**Input**: `N = 6, K = 1, W = 10`
**Output**: `0.60000`
**Explanation**:  Alice gets a single card, then stops. In 6 out of W = 10 possibilities, she is at or below N = 6 points.

**Example 3**:
**Input**: `N = 21, K = 17, W = 10`
**Output**: `0.73278`

**Note**:
- `0 <= K <= N <= 10000`
- `1 <= W <= 10000`
- Answers will be accepted as correct if they are within `10^-5` of the correct answer.
- The judging time limit has been reduced for this question.


## Solution

### Solution-1

```python {class=line-numbers}
class Solution(object):
    def new21Game(self, N, K, W):
        """
        :type N: int
        :type K: int
        :type W: int
        :rtype: float
        """
        if N>=K-1+W: return 1.0
        if N<K: return 0.0
        record = [0]*(K+W)
        record[0] = 1.0
        for i in range(1, len(record)):
            record[i] = 1.0*sum(record[max(0, i-W):min(i, K)])/W
        return sum(record[K:N+1])
```

上面的代码在 `for` 循环中的 `sum` 函数的执行在 W 较大时，非常费时，在本地测试 `N=9632, K=6575, W=5602` 时，运行时间为：239ms，下面的代码对其进行了优化。

### Solution-2
```python{class=line-numbers}
class Solution(object):
    def new21Game(self, N, K, W):
        """
        :type N: int
        :type K: int
        :type W: int
        :rtype: float
        """
        if N>=K-1+W: return 1.0
        if N<K: return 0.0
        record = [0]*(K+W)
        record[0], sum_value = 1.0, 0
        for i in range(1, len(record)):
            if i-W>0:
                sum_value -= record[i-W-1]
            if i<=K:
                sum_value += record[i-1]
            record[i] = 1.0*sum_value/W
        return sum(record[K:N+1])
```
同样，输入为 `N=9632, K=6575, W=5602`，运行时间为：2.87ms，速度加快了 100 倍左右。
