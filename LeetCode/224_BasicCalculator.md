---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 224. Basic Calculator

> <https://leetcode.com/problems/basic-calculator/description/>

## Description

Implement a basic calculator to evaluate a simple expression string.

The expression string may contain open `(` and closing parentheses `)`, the plus `+` or minus sign `-`, non-negative integers and empty spaces ` `.

**Example 1**:
>**Input**: "1 + 1"
**Output**: 2

**Example 2**:
>**Input**: " 2-1 + 2 "
**Output**: 3

**Example 3**:
>**Input**: "(1+(4+5+2)-3)+(6+8)"
**Output**: 23

**Note**:
- You may assume that the given expression is always valid.
- Do not use the `eval` built-in library function.


## Solution

### Solution-1

一个能处理加、减、乘、除、阶乘、括号的函数，数字可以为小数或整数：
```python {class="line-numbers"}
class Solution:
    def getOptFunc(self, opt_name):
        opt_dict = {
            '+' : lambda a,b:a+b,
            '-' : lambda a,b:a-b,
            '*' : lambda a,b:a*b,
            '/' : lambda a,b:a/b,
            '^' : lambda a,b:a**b,
        }
        return opt_dict[opt_name]
    def calculate(self, s):
        """
        :type s: str
        :rtype: int
        """
        import re
        # 创建符号的正则表达式
        opt_rank = {')':0, '+':1, '-':1, '*':2, '/':2, '^':3, '(':10}
        opt_reg = r'([\+|\-|\*|/|\^|\(|\)])'
        num_reg = r'(\d+\.?\d*)'
        item_reg = re.compile(opt_reg+'|'+num_reg)
        opt_stack = []
        num_stack = []
        str_express = '({})'.format(s)
        for i in item_reg.finditer(str_express):
            i = i.groups()
            if i[1]:
                # 匹配到操作符处理
                if '.' in i[1]:
                    num_stack.append(float(i[1]))
                else:
                    num_stack.append(int(i[1]))
            else:
                while (len(opt_stack) and opt_stack[-1]!='('
                       and opt_rank[i[0]]<=opt_rank[opt_stack[-1]]):
                    b = num_stack.pop()
                    a = num_stack.pop()
                    opt_fun = self.getOptFunc(opt_stack.pop())
                    num_stack.append(opt_fun(a, b))
                opt_stack.append(i[0])
                if opt_stack[-1]==')':
                    del opt_stack[-2:]
        return num_stack[-1]
```
优点：功能强大，而不是仅仅能处理加减运算
缺点：LeetCode 上的测试速度为 300 ms。

### Solution-2

只处理加减和整数：

```python {class="line-numbers"}
class Solution:
    def calculate(self, s):
        """
        :type s: str
        :rtype: int
        """
        s = '(' + s + ')'
        opts = []
        nums = []
        opt_funcs = {'+':lambda a,b:a+b, '-':lambda a,b:a-b}
        i = 0
        while i < len(s):
            if s[i]==' ':
                i += 1
                continue
            num_begin = num_end = i
            while i<len(s) and '0' <= s[i] <= '9':
                num_end += 1
                i += 1
            if num_begin != num_end:
                nums.append(int(s[num_begin:num_end]))
                continue
            if s[i]=='(':
                opts.append('(')
            elif s[i] in ('+', '-'):
                if len(opts) and opts[-1]!='(':
                    right_v = nums.pop()
                    left_v = nums.pop()
                    nums.append(opt_funcs[opts.pop()](left_v, right_v))
                opts.append(s[i])
            elif s[i]==')':
                if len(opts) and opts[-1]=='(':
                    opts.pop()
                else:
                    right_v = nums.pop()
                    left_v = nums.pop()
                    nums.append(opt_funcs[opts.pop()](left_v, right_v))
                    opts.pop()
            i += 1
        return nums[-1]
```


