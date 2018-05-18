---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 72. Edit Distance

> <https://leetcode.com/problems/edit-distance/description/>

## Description

Given two words word1 and word2, find the minimum number of operations required to convert word1 to word2.

You have the following 3 operations permitted on a word:

- Insert a character
- Delete a character
- Replace a character

**Example 1**:

> **Input**: word1 = "horse", word2 = "ros"
**Output**: 3
**Explanation**: 
horse -> rorse (replace 'h' with 'r')
rorse -> rose (remove 'r')
rose -> ros (remove 'e')

**Example 2**:

> **Input**: word1 = "intention", word2 = "execution"
**Output**: 5
**Explanation**: 
intention -> inention (remove 't')
inention -> enention (replace 'i' with 'e')
enention -> exention (replace 'n' with 'x')
exention -> exection (replace 'n' with 'c')
exection -> execution (insert 'u')

## Solutions

### 爬山法(选择最优的点)

```python
class Solution:
    def minDistance(self, word1, word2):
        """
        :type word1: str
        :type word2: str
        :rtype: int
        """
        from itertools import chain
        # heuristic function
        from difflib import SequenceMatcher
        # replace charactor string generator
        def replaceGenerator(word1, word2):
            for i in range(min(len(word1), len(word2))):
                if word1[i] == word2[i]:
                    continue
                yield word1[:i]+word2[i]+word1[i+1:]
                
        # delete charactor string generator
        def deleteGenerator(word1):
            for i in range(len(word1)):
                yield word1[:i]+word1[i+1:]
                
        # insert charactor string generator
        def insertGenerator(word1, word2):
            for i in range(len(word1)+1):
                if i < len(word2):
                    yield word1[:i]+word2[i]+word1[i:]
                
        min_d = 0
        while word1!=word2:
            cur_ratio = SequenceMatcher(None, word1, word2).ratio()
            max_improve = 0
            best_next_w1 = word1
            for tp_word1 in chain(replaceGenerator(word1, word2),
                    deleteGenerator(word1), insertGenerator(word1, word2)):
                tp_ratio = SequenceMatcher(None, tp_word1, word2).ratio()
                if tp_ratio-cur_ratio > max_improve:
                    max_improve = tp_ratio-cur_ratio
                    best_next_w1 = tp_word1
            word1 = best_next_w1
#             print(word1)
            min_d += 1
        return min_d
```

优点：速度快
缺点：可能找到的不是最优解

例如：
```python
>>> s = Solution()
>>> s.minDistance("dinitrophenylhydrazine",
    "acetylphenylhydrazine")
7
```
实际应该为6，可是得到的结果为7，而且因为每次都选择最优的，所以每次的结果都一样。一种解决思路是使用首优搜索，并使用多次重启爬山法。但是这个题目有点特殊，因为每次换一个字符，一定比之前的字符串匹配度高，所以首优并没有意义。

再来看看其时间效率：
```python
>>> %time s.minDistance(
    "pneumonoultramicroscopicsilicovolcanoconiosis", 
    "stereomicroscopically")
CPU times: user 130 ms, sys: 2.71 ms, total: 133 ms
Wall time: 132 ms
30
```
其时间效率还好。

### 局部束搜索

```python
class Solution:
    def minDistance(self, word1, word2):
        """
        :type word1: str
        :type word2: str
        :rtype: int
        """
        from itertools import chain
        from collections import namedtuple
        # heuristic function
        from difflib import SequenceMatcher
        # replace charactor string generator
        def replaceGenerator(word1, word2):
            for i in range(min(len(word1), len(word2))):
                if word1[i] == word2[i]:
                    continue
                yield word1[:i]+word2[i]+word1[i+1:]
                
        # delete charactor string generator
        def deleteGenerator(word1, word2):
            for i in range(len(word1)):
                if i<len(word2) and word1[i]==word2[i]:
                    continue
                yield word1[:i]+word1[i+1:]
                
        # insert charactor string generator
        def insertGenerator(word1, word2):
            for i in range(len(word1)+1):
                if i < len(word2):
                    yield word1[:i]+word2[i]+word1[i:]
                
        min_d = 0
        Beam = namedtuple('Beam', 'ratio word1')
        beams = [Beam(0, word1)]
        max_beams_cnt = 3
        visited = {beams[0].word1}
        exploded = set()
        while beams[0].word1!=word2:
            tp_beams = beams[:]
            for beam in tp_beams:
                word1 = beam.word1
                if word1 in exploded:
                    continue
                else:
                    exploded.add(word1)
                cur_ratio = SequenceMatcher(None, word1, word2).ratio()
                for tp_word1 in chain(replaceGenerator(word1, word2),
                        deleteGenerator(word1, word2), insertGenerator(word1, word2)):
                    if tp_word1 in visited:
                        continue
                    else:
                        visited.add(tp_word1)
                    tp_ratio = SequenceMatcher(None, tp_word1, word2).ratio()
                    if tp_ratio-cur_ratio > 0:
                        tp_beam = Beam(tp_ratio, tp_word1)
                        for i in range(max_beams_cnt):
                            if i >= len(beams):
                                beams.append(tp_beam)
                                break
                            if beams[i].ratio < tp_beam.ratio:
                                beams.insert(i, tp_beam)
                                break
            min_d += 1
        return min_d
```
正确性验证，还是用上面那个例子：
```python
>>> s.minDistance("dinitrophenylhydrazine",
        "acetylphenylhydrazine")
6
```
正确性的问题改善了，不过并没有完全解决！

看看时间复杂度：
```python
>>> %time s.minDistance(
    "pneumonoultramicroscopicsilicovolcanoconiosis", 
    "stereomicroscopically")

CPU times: user 359 ms, sys: 3.94 ms, total: 363 ms
Wall time: 378 ms
30
```
时间复杂度不是很高，不过 LeetCode上就是通不过 Time Limit，可能是用了太多高级的类和函数吧。

### Solution 3

最后上一个大神写的代码：
```python
class Solution:
    def minDistance(self, word1, word2):
        """
        :type word1: str
        :type word2: str
        :rtype: int
        """
        a, b = len(word1)-1, len(word2)-1
        mem = {}
        
        def dist(a, b):
            if a == -1:
                return b+1
            if b == -1:
                return a+1
            if (a,b) in mem:
                return mem[(a,b)]
            if word1[a] == word2[b]:
                d = dist(a-1, b-1)
            else:
            # dist(a-1,b-1)表示replace
            # dist(a-1,b)表示delete
            # dist(a,b-1)表示insert
                d = min(dist(a-1, b-1), dist(a-1, b), dist(a, b-1)) + 1
            mem[(a,b)] = d
            return d
        
        return dist(a,b)
```
评价：都知道动态规划的问题基本上要用递归，不过关键是要知道如何分治。

测试结果：
```python
>>> %time s.minDistance(
    "pneumonoultramicroscopicsilicovolcanoconiosis", 
    "stereomicroscopically")
CPU times: user 353 ms, sys: 2.6 ms, total: 356 ms
Wall time: 358 ms
30
```
速度和上面的局部束搜索差不多，比爬山法还慢一点。不过就是通过了 Time Limit。

基本上来说，爬山法都是最快的。
