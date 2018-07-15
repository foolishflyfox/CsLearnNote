---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 808. Soup Servings

> <https://leetcode.com/problems/soup-servings/description/>

## Description

There are two types of soup: type A and type B. Initially we have `N` ml of each type of soup. There are four kinds of operations:

- Serve 100 ml of soup A and 0 ml of soup B
- Serve 75 ml of soup A and 25 ml of soup B
- Serve 50 ml of soup A and 50 ml of soup B
- Serve 25 ml of soup A and 75 ml of soup B

When we serve some soup, we give it to someone and we no longer have it.  Each turn, we will choose from the four operations with equal probability 0.25. If the remaining volume of soup is not enough to complete the operation, we will serve as much as we can.  We stop once we no longer have some quantity of both types of soup.

Note that we do not have the operation where all 100 ml's of soup B are used first.  

Return the probability that soup A will be empty first, plus half the probability that A and B become empty at the same time.

**Example**:
**Input**: `N = 50`
**Output**: `0.625`
**Explanation**: 
If we choose the first two operations, A will become empty first. For the third operation, A and B will become empty at the same time. For the fourth operation, B will become empty first. So the total probability of A becoming empty first plus half the probability that A and B become empty at the same time, is 0.25 * (1 + 1 + 0.5 + 0) = 0.625.

Notes:
- `0 <= N <= 10^9`. 
- Answers within `10^-6` of the true value will be accepted as correct.

## Solution

使用递归调用求解概率，代码如下：

```python
class Solution(object):
    def get_possbility(self, a_N, b_N):
        if (a_N, b_N) in self.record:
            return self.record[(a_N, b_N)]
        if a_N==0 and b_N!=0: return 1
        if a_N!=0 and b_N==0: return 0
        a1,b1 = max(0, a_N-100), b_N
        a2,b2 = max(0, a_N-75), max(0, b_N-25)
        a3,b3 = max(0, a_N-50), max(0, b_N-50)
        a4,b4 = max(0, a_N-25), max(0, b_N-75)
        p2 = self.get_possbility(a2, b2)
        p3 = self.get_possbility(a3, b3)
        p4 = self.get_possbility(a4, b4)
        if a1==a_N:
            p = (p2+p3+p4)/3
        else:
            p1 = self.get_possbility(a1, b1)
            p = 0.25*(p1+p2+p3+p4)
        self.record[(a_N, b_N)] = p
        return p
    def soupServings(self, N):
        """
        :type N: int
        :rtype: float
        """
        self.record = {(0, 0):0.5}
        return self.get_possbility(N, N)
```
上述代码在 `N=660295675` 由于递归的层数过多，会造成栈错误；

由于题目要求误差小于 $10^{-6}$ 即可，而我们通过下面代码：
```python
s = Solution()
for i in range(1000, 7001, 1000):
    print(s.soupServings(i))
```
输出为：
```
0.9765650521094358
0.9977163163248763
0.9997529725570644
0.9999719963014364
0.9999967386599964
0.9999996134414416
0.9999999536104321
```
在 N=6000 时，输出大于 $0.999999$，并且 N 越大，输出的值也越大，即当 N>=6000 时，输出 1 即可，修正后的代码为：
```python
class Solution(object):
    def get_possbility(self, a_N, b_N):
        if (a_N, b_N) in self.record:
            return self.record[(a_N, b_N)]
        if a_N==0 and b_N!=0: return 1
        if a_N!=0 and b_N==0: return 0
        a1,b1 = max(0, a_N-100), b_N
        a2,b2 = max(0, a_N-75), max(0, b_N-25)
        p2 = self.get_possbility(a2, b2)
        a3,b3 = max(0, a_N-50), max(0, b_N-50)
        p3 = self.get_possbility(a3, b3)
        a4,b4 = max(0, a_N-25), max(0, b_N-75)
        p4 = self.get_possbility(a4, b4)
        if a1==a_N:
            p = (p2+p3+p4)/3
        else:
            p1 = self.get_possbility(a1, b1)
            p = 0.25*(p1+p2+p3+p4)
        self.record[(a_N, b_N)] = p
        return p
    def soupServings(self, N):
        """
        :type N: int
        :rtype: float
        """
        self.record = {(0, 0):0.5}
        if N >= 6000: return 1
        return self.get_possbility(N, N)
```
