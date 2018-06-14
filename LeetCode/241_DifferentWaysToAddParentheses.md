---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 241. Different Ways to Add Parentheses

> <https://leetcode.com/problems/different-ways-to-add-parentheses/description/>

## Description

Given a string of numbers and operators, return all possible results from computing all the different possible ways to group numbers and operators. The valid operators are `+`, `-` and `*`.

**Example 1**:
>**Input**: "2-1-1"
**Output**: [0, 2]
**Explanation**: 
`((2-1)-1) = 0 `
`(2-(1-1)) = 2`

**Example 2**:
>**Input**: "2*3-4*5"
**Output**: [-34, -14, -10, -10, 10]
**Explanation**: 
`(2*(3-(4*5))) = -34 `
`((2*3)-(4*5)) = -14 `
`((2*(3-4))*5) = -10 `
`(2*((3-4)*5)) = -10 `
`(((2*3)-4)*5) = 10`

## Solution

表达式可以视为一个中序遍历，我们通过中序遍历构造一棵运算树，其中的每个非叶子节点都是操作符，每个叶子节点都是操作数，每个构造的树都是一个解，通过递归的方式进行运算树的构建，在构造过程中直接进行运算，而不是正在完成一棵构造树之后再进行运算：
```python {class="line-numbers"}
import re
import operator
class Solution:
    def get_all_answer(self, elements):
        if len(elements)==1:
            return [elements[0]]
        if len(elements)==3:
            return [elements[1](elements[0], elements[2])]
        i = 1
        ret = []
        while i < len(elements):
            l_ret = self.get_all_answer(elements[:i])
            r_ret = self.get_all_answer(elements[i+1:])
            for l_v in l_ret:
                for r_v in r_ret:
                    ret.append(elements[i](l_v, r_v))
            i += 2
        return ret
    def diffWaysToCompute(self, input):
        """
        :type input: str
        :rtype: List[int]
        """
        elements = []
        reg = r"(\d+)|(\+|\-|\*)"
        for ele in re.finditer(reg, input):
            if str.isnumeric(ele.group()):
                elements.append(int(ele.group()))
            else:
                if ele.group()=='+':
                    elements.append(operator.add)
                elif ele.group()=='-':
                    elements.append(operator.sub)
                else:
                    elements.append(operator.mul)
        if len(elements)==0:return None
        return self.get_all_answer(elements)
```
运算速度 40 ms，由于 99% 的提交。
