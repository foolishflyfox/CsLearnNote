---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 869. Reordered Power of 2

> <https://leetcode.com/problems/reordered-power-of-2/description/>

## Description

Starting with a positive integer `N`, we reorder the digits in any order (including the original order) such that the leading digit is not zero.

Return `true` if and only if we can do this in a way such that the resulting number is a power of 2.

 


**Example 1**:
**Input**: `1`
**Output**: `true`

**Example 2**:
**Input**: `10`
**Output**: `false`

**Example 3**:
**Input**: `16`
**Output**: `true`

**Example 4**:
**Input**: `24`
**Output**: `false`

**Example 5**:
**Input**: `46`
**Output**: `true`

## Solution

```python
class Solution(object):
    def reorderedPowerOf2(self, N):
        """
        :type N: int
        :rtype: bool
        """
        from collections import Counter
        record = {
        (('1',2),('2',2),('3',1),('4',1),('7',2),('8',1)),
        (('1',1),('2',1),('8',1)),
        (('4',1),),
        (('2',1),),
        (('2',2),('4',1),('5',1),('8',2)),
        (('1',1),('6',1)),
        (('2',1),('3',1),('6',1),('7',1),('8',1)),
        (('1',2),('2',1),('6',2),('7',3)),
        (('0',1),('4',1),('6',1),('9',1)),
        (('2',1),('5',1),('6',1)),
        (('0',1),('2',1),('4',1),('8',1)),
        (('4',1),('6',1)),
        (('0',1),('1',1),('2',1),('4',1)),
        (('1',1),('2',1),('8',1),('9',1)),
        (('0',1),('1',1),('4',1),('6',2),('7',1),('8',2)),
        (('0',1),('1',2),('2',1),('3',1),('7',1)),
        (('0',1),('1',1),('4',1),('5',1),('6',1),('7',1),('8',1)),
        (('3',1),('5',2),('6',2)),
        (('2',1),('3',1),('4',2),('5',2),('6',2),('8',1)),
        (('0',1),('1',1),('3',1),('4',3),('9',1)),
        (('1',1),),
        (('1',1),('2',1),('5',1)),
        (('2',1),('3',3),('4',2),('5',2)),
        (('1',1),('2',2),('4',2),('6',1)),
        (('8',1),),
        (('1',1),('3',1),('4',1),('6',1),('8',1)),
        (('0',1),('3',1),('6',1),('8',4)),
        (('0',1),('1',1),('2',2),('5',1),('7',1),('9',1)),
        (('2',1),('3',1)),
        (('0',1),('1',1),('2',1),('3',1),('5',1),('6',1),('7',1),('8',1),('9',1))
        }
        return tuple(sorted(list(Counter(str(N)).items()))) in record
```

其中，`record` 变量是通过下列代码产生的：
```python
from collections import Counter
a = []
i = 0
while True:
    if 2**i<=10**9:
        a.append(str(2**i))
    else:
        break
    i += 1
record = set([tuple(sorted(list(Counter(i).items()))) for i in a])
print(record)
```

