---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 648. Replace Words

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

My solution:
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

## Analysis

