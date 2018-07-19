---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 401. Binary Watch

> <https://leetcode.com/problems/binary-watch/description/>

## Description

A binary watch has 4 LEDs on the top which represent the **hours (0-11)**, and the 6 LEDs on the bottom represent the **minutes (0-59)**.

Each LED represents a zero or one, with the least significant bit on the right.

![](/LeetCode/Binary_clock_samui_moon.jpg)
<small>For example, the above binary watch reads "3:25".</small>

Given a non-negative integer n which represents the number of LEDs that are currently on, return all possible times the watch could represent.

**Example**:
**Input**: `n = 1`
**Return**: `["1:00", "2:00", "4:00", "8:00", "0:01", "0:02", "0:04", "0:08", "0:16", "0:32"]`
**Note**:
- The order of output does not matter.
- The hour must not contain a leading zero, for example "01:00" is not valid, it should be "1:00".
- The minute must be consist of two digits and may contain a leading zero, for example "10:2" is not valid, it should be "10:02".

## Solution

测试用时：20 ms
```python
from itertools import permutations
class Solution(object):
    def get_hours(self, n):
        if n>=4: return []
        return {0:['0'], 1:['1', '2', '4', '8'], 3:['7', '11'],
         2:['3', '5', '6', '9', '10'],}[n]
    def get_minutes(self, n):
        if n>=6: return []
        return {0:['00'], 1:['01', '02', '04', '08', '16', '32'],
         2:['03', '05', '09', '17', '33', '06', '10', '18',
            '34', '12', '20', '36', '24', '40', '48'],
         3:['07', '11', '19', '35', '13', '21', '37', '25',
            '41', '49', '14', '22', '38', '26', '42', '50',
            '28', '44', '52', '56'],
         4:['15', '23', '39', '27', '43', '51', '29', '45', 
            '53', '57', '30', '46', '54', '58'],
         5:['31', '47', '55', '59']}[n]
    def readBinaryWatch(self, num):
        """
        :type num: int
        :rtype: List[str]
        """
        ret = []
        for i in range(0, num+1):
            for j in self.get_hours(i):
                for k in self.get_minutes(num-i):
                    ret.append(j+":"+k)
        return ret
```


