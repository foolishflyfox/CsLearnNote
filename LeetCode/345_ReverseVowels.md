---
export_on_save:
 html: true
---
# 345. Reverse Vowels of a String

Write a function that takes a string as input and reverse only the vowels of a string.

**Example 1:**
Given s = "hello", return "holle".

**Example 2:**
Given s = "leetcode", return "leotcede".

**Note:**
The vowels does not include the letter "y".

---

My solution:

```python {.line-numbers}
class Solution:
    def reverseVowels(self, s):
        """
        :type s: str
        :rtype: str
        """
        s = list(s)
        s_len = len(s)
        p_left = 0
        p_right = s_len-1
        vowels = list("aeiouAEIOU")
        while p_left < p_right:
            while (p_left < p_right) and (s[p_left] not in vowels):
                p_left += 1
            while (p_left < p_right) and (s[p_right] not in vowels):
                p_right -= 1
            if p_left < p_right:
                s[p_left], s[p_right] = s[p_right], s[p_left]
                p_left += 1
                p_right -= 1
        return ''.join(s)
```


