---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 583. Delete Operation for Two Strings

> <https://leetcode.com/problems/delete-operation-for-two-strings/description/>

对最长公共子序列的计算分析见：<https://blog.csdn.net/hrn1216/article/details/51534607>

## Description

Given two words *word1* and *word2*, find the minimum number of steps required to make *word1* and *word2* the same, where in each step you can delete one character in either string.

**Example 1**:

**Input**: `"sea", "eat"`
**Output**: `2`
**Explanation**: You need one step to make "sea" to "ea" and another step to make "eat" to "ea".

**Note**:
- The length of given words won't exceed 500.
- Characters in given words can only be lower-case letters.

## Solution

### Solution-1

用递归的动态规划（DP）的方式求解：

```python
class Solution(object):
    def LCS(self, len1, len2):
        if len1==0 or len2==0: 
            return 0
        if (len1, len2) in self.record:
            return self.record[(len1, len2)]
        if self.word1[len1-1]==self.word2[len2-1]:
            ret = self.LCS(len1-1, len2-1)+1
            self.record[(len1, len2)] = ret
        else:
            ret = max(self.LCS(len1-1, len2),
                     self.LCS(len1, len2-1))
            self.record[(len1, len2)] = ret
        return ret
    def minDistance(self, word1, word2):
        """
        :type word1: str
        :type word2: str
        :rtype: int
        """
        self.record = dict()
        self.word1, len1 = word1, len(word1)
        self.word2, len2 = word2, len(word2)
        return len1+len2-2*self.LCS(len1, len2)
```
### Solution-2

非递归方式：
```python
class Solution(object):
    def minDistance(self, word1, word2):
        """
        :type word1: str
        :type word2: str
        :rtype: int
        """
        len1, len2 = len(word1), len(word2)
        record = [[0]*(len2+1) for i in range(len1+1)]
        for i in range(1, len1+1):
            for j in range(1, len2+1):
                if word1[i-1]==word2[j-1]: 
                    record[i][j] = record[i-1][j-1]+1
                else:
                    record[i][j] = max(record[i-1][j], record[i][j-1])
        return len1+len2-2*record[len1][len2]
```

