---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 151. Reverse Words in a String
> <https://leetcode.com/problems/reverse-words-in-a-string/description/>

## Description

Given an input string, reverse the string word by word.

**Example**:  

>**Input**: "the sky is blue",
**Output**: "blue is sky the".

**Note**:
- A word is defined as a sequence of non-space characters.
- Input string may contain leading or trailing spaces. However, your reversed string should not contain leading or trailing spaces.
- You need to reduce multiple spaces between two words to a single space in the reversed string.

**Follow up**: For C programmers, try to solve it in-place in O(1) space.

## Solution

### Solution-1

以遇到空格为触发事件，进行数据修改(不需要看下面的代码)：
```python {class=line-numbers}
class Solution(object):
    def reverse_recusive(self, sa, left, right):
        while left < right and sa[left]!=' ' and sa[right]!=' ':
            sa[left], sa[right] = sa[right], sa[left]
            left += 1
            right -= 1
        left_c, right_c = sa[left], sa[right]
        if left_c!=' ' and right_c!=' ':
            pre_left, pre_right = left, right
            while pre_left>=0 and pre_left!=' ': pre_left -= 1
            while pre_right<self.s_len and pre_right!=' ': pre_right += 1
            pre_left, pre_right = pre_left+1, pre_right-1
            while pre_left<pre_right:
                sa[pre_left], sa[pre_right] = sa[pre_right], sa[pre_left]
                pre_left += 1
                pre_right -= 1
            return
        if right_c==' ':
            sa[right], sa[left] = sa[left], sa[right]
            tp_left = left - 1
            pre_left = tp_left
            while pre_left>=0 and pre_left!=' ':
                pre_left -= 1
            pre_left += 1
            while pre_left < tp_left:
                sa[tp_left], sa[pre_left] = sa[pre_left], sa[tp_left]
                tp_left -= 1
                pre_left += 1
        if left_c==' ':
            sa[right], sa[left] = sa[left], sa[right]
            tp_right = right + 1
            pre_right = tp_right
            while pre_right<self.s_len and pre_right!=' ':
                pre_right += 1
            pre_right -= 1
            tp_right = tp_right
            while pre_right > tp_right:
                sa[tp_right], sa[pre_right] = sa[pre_right], sa[tp_right]
                tp_right += 1
                pre_right -= 1
        left += 1
        right -= 1
        self.reverse_recusive(sa, left, right)
            
    def reverseWords(self, s):
        """
        :type s: str
        :rtype: str
        """
        sa = list(s.strip())
        self.s_len = len(sa)
        self.reverse_recusive(sa, 0, self.s_len-1)
        return sa
```
思路简单，但是实际操作时逻辑比较复杂，没有调试出来；

### Solution-2

首先将整个字符串转置，然后再切分转换：
```python {class=line-numbers}
class Solution(object):
    def reverseWords(self, s):
        """
        :type s: str
        :rtype: str
        """
        sa = list(s.strip())
        sa_len = len(sa)
        left, right = 0, sa_len-1
        while left<right:
            sa[left], sa[right] = sa[right], sa[left]
            left += 1
            right -= 1
        part_left = -1
        part_right = 0
        while part_right<sa_len:
            while part_right<sa_len and sa[part_right]!=' ':
                part_right += 1
            else:
                tp_left, tp_right = part_left+1, part_right-1
                while tp_left<tp_right:
                    sa[tp_left], sa[tp_right] = sa[tp_right], sa[tp_left]
                    tp_left += 1
                    tp_right -= 1
                part_left = part_right
                part_right += 1
            while part_right<sa_len and sa[part_right]==' ':
                del sa[part_right]
                sa_len -= 1
        return ''.join(sa)
```
上述算法比之前的简单很多。如果一个问题需要实现多个功能，最好是将功能拆分后一个一个地去实现，一起来实现会试逻辑非常复杂。

上述代码的空间复杂度为 $O(1)$。


### Solution-3

其实 Python3 内置的很多函数可以用，可以只使用一个语句完成上述功能

```python
class Solution(object):
    def reverseWords(self, s):
        """
        :type s: str
        :rtype: str
        """
        return ' '.join(reversed(s.split()))
```
