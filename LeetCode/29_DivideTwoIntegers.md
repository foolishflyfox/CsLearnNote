---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 29. Divide Two Integers

> <https://leetcode.com/problems/divide-two-integers/description/>

## Description


Given two integers `dividend` and `divisor`, divide two integers without using multiplication, division and mod operator.

Return the quotient after dividing `dividend` by `divisor`.

The integer division should truncate toward zero.

**Example 1**:
- Input: `dividend = 10, divisor = 3`
- Output: `3`

**Example 2**:
- Input: `dividend = 7, divisor = -3`
- Output: `-2`

**Note**:
- Both dividend and divisor will be 32-bit signed integers.
- The divisor will never be 0.
- Assume we are dealing with an environment which could only store integers within the 32-bit signed integer range: $[-2^{31},2^{31}-1]$. For the purpose of this problem, assume that your function returns $2^{31}-1$ when the division result overflows.

## Solution

```cpp {class=line-numbers}
class Solution {
public:
    int divide(int dividend, int divisor) {
        if(dividend==divisor){
            return 1;
        }
        // divident=-2^31 , divisor=-1时，相除会越界
        if(!(dividend^0x80000000) && (divisor==-1)){
            return 0x7fffffff;
        }
        // divisor=-2^31 作为除数，并且 divident与其不相等
        // 返回必为 0
        if(!(divisor^0x80000000)){
            return 0;
        }
        int sign = 1;
        int ret = 0;
        // 确认除数与被除数是否异号
        if((dividend&0x80000000)^(divisor&0x80000000)){
            sign = -1;
        }
        // 相当于 divisor = abs(divisor)
        if(divisor&0x80000000){
            divisor = (~divisor)+1;
        }
        if(!(dividend^0x80000000)){
            ret += 1;
            dividend += divisor;
        }
        // 相当于 dividend = abs(dividend)
        if(dividend&0x80000000){
            dividend = (~dividend)+1;
        }
        int i = 31;
        // 获取除数的最高的有效位
        while(!(divisor & (1<<i))){
            --i;
        }
        // 使用类似辗转相除法
        for(int j=30-i;j>=0;--j){
            while((divisor<<j)<=dividend){
                dividend -= (divisor<<j);
                ret += (1<<j);
            }
        }
        if(sign==-1){
            // 取相反数：ret = -ret
            ret = (~ret)+1;
        }
        return ret;
    }
};
```

