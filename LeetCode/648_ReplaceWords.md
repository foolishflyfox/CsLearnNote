---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 648. Replace Words
> <https://leetcode.com/problems/replace-words/>
字典搜索问题
## Problem description

In English, we have a concept called `root`, which can be followed by some other words to form another longer word - let's call this word `successor`. For example, the root `an`, followed by `other`, which can form another word `another`.

Now, given a dictionary consisting of many roots and a sentence. You need to replace all the `successor` in the sentence with the `root` forming it. If a `successor` has many `roots` can form it, replace it with the root with the shortest length.

You need to output the sentence after the replacement.

> **Example 1:**
**Input:** dict = ["cat", "bat", "rat"]
sentence = "the cattle was rattled by the battery"
**Output:** "the cat was rat by the bat"

**Note:**
1. The input will only have lower-case letters.
1. 1 <= dict words number <= 1000
1. 1 <= sentence words number <= 1000
1. 1 <= root length <= 100
1. 1 <= sentence words length <= 1000

---

## Solutions

My solution :
```python {.line-numbers}
class Solution:
    def replaceWords(self, dict, sentence):
        """
        :type dict: List[str]
        :type sentence: str
        :rtype: str
        """
        comp_dict = {}
        for word in dict:
            cur_dir = comp_dict
            for letter in word:
                if letter in cur_dir:
                    cur_dir = cur_dir[letter]
                    if 0 in cur_dir:
                        break
                else:
                    cur_dir[letter] = {}
                    cur_dir = cur_dir[letter]
            cur_dir[0]=0
        r_sentence = ""
        p = 0
        s_len = len(sentence)
        while p < s_len:
            while p < s_len and not ('a'<=sentence[p]<='z'):
                r_sentence += sentence[p]
                p += 1
            tmp_word = ""
            while p < s_len and ('a'<=sentence[p]<='z'):
                tmp_word += sentence[p]
                p += 1
            if len(tmp_word)==0:
                break
            in_word = ""
            cur_dir = comp_dict
            unappend = True
            for letter in tmp_word:
                if letter not in cur_dir:
                    r_sentence += tmp_word
                    unappend = False
                    break
                else:
                    in_word += letter
                    cur_dir = cur_dir[letter]
                    if 0 in cur_dir:
                        r_sentence += in_word
                        unappend = False
                        break
            if unappend:
                r_sentence += tmp_word
        return r_sentence
```

Other's more elegent solution:

- solution 1:

```python {.line-numbers}
def replaceWords(self, roots, sentence):
    _trie = lambda: collections.defaultdict(_trie)
    trie = _trie()
    END = True
    for root in roots:
        cur = trie
        for letter in root:
            cur = cur[letter]
        cur[END] = root

    def replace(word):
        cur = trie
        for letter in word:
            if letter not in cur: break
            cur = cur[letter]
            if END in cur:
                return cur[END]
        return word

    return " ".join(map(replace, sentence.split()))
```

- solution 2:
```python
class Solution(object):
    def replaceWords(self, dict, sentence):
        """
        :type dict: List[str]
        :type sentence: str
        :rtype: str
        """
        setenceAsList = sentence.split(" ")
        for i in range(len(setenceAsList)):
            for j in dict:
                if setenceAsList[i].startswith(j):
                    setenceAsList[i] = j
        return " ".join(setenceAsList)
```

## Analysis

我自己的算法的效率测试为：
```
286µs ± 9.8µs per loop (mean ± std. dev. of 7 runs, 1000 loops each)
```

solution 1 的效率测试为：
```
116µs ± 2.43µs per loop(mean ± std. dev. of 7 runs, 10000 loops each)
```

solution 2 的效率测试为：
```
1.06ms ± 20.5µs per loop (mean ± std. dev. of 7 runs, 1000 loops each)
```

可以看出 solution 2 的算法代码最简单，但是效率非常低。

solution 1 的效率最高，代码也较为简洁，应该是3个中最好的算法，但是需要引入额外的 `collection` 库。

该程序中使用了 `collection` 库的 `defaultdict` 类。

我的程序虽然效率不如 solution 2，长度也较长，但是其适应性更好，能够保留多个空格以及非小写字母 。

## Conclusion

`str`类型的元素有一个函数：`startswith`，用于判断元素是否以特定字符串开始；

