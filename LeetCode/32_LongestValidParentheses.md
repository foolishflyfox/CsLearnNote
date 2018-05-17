---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 32. Longest Valid Parentheses
> <https://leetcode.com/problems/longest-valid-parentheses/description/>

## Description

Given a string containing just the characters `(` and `)`, find the length of the longest valid (well-formed) parentheses substring.

**Example 1**:
>**Input**: "(()"
**Output**: 2
**Explanation**: The longest valid parentheses substring is "()"

**Example 2**:
>**Input**: ")()())"
**Output**: 4
**Explanation**: The longest valid parentheses substring is "()()"

## Solution

### Solution-1

```python
class Solution:
    def longestValidParentheses(self, s):
        """
        :type s: str
        :rtype: int
        """
        longest_ps = 0
        cur_ps = 0
        record = 0
        queue = []
        for c in s:
            queue.append(c)
            if c=='(':
                record += 1
            else:
                if record>0:
                    record -= 1
                    cur_ps += 2
                    if record==0 and cur_ps>longest_ps:
                        longest_ps = cur_ps
                else:
                    if cur_ps > longest_ps:
                        longest_ps = cur_ps
                    cur_ps = 0
                    queue.clear()
        cur_ps = 0
        record = 0
        for c in queue[::-1]:
            if c==')':
                record += 1
            else:
                if record>0:
                    record -= 1
                    cur_ps += 2
                    if record==0 and cur_ps>longest_ps:
                        longest_ps = cur_ps
                else:
                    if cur_ps > longest_ps:
                        longest_ps = cur_ps
                    cur_ps = 0
        
        return longest_ps
```

效率高，LeetCode上测试效率高于 99.3% 的代码。

### Solution-2

```python
class Solution(object):
    def longestValidParentheses(self, s):
        """
        :type s: str
        :rtype: int
        """
        st = []
        max_l = 0
        l = 0
        for i, x in enumerate(s):
            l += 1
            
            # pushs and pops to and from stack
            if x == "(":
                st.append(i)
            else:
                if st:
                    st.pop()
                else:
                    # no hope for being acceptable. Restart counting.
                    l = 0
            
            if st:
                # track strings that have acceptable substrings
                max_l = max(max_l, i-st[-1])
            else:
                # track acceptable strings
                max_l = max(max_l, l)
                
        return max_l
```

