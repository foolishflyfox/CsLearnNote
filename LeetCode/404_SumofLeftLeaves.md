---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 404. Sum of Left Leaves

> <https://leetcode.com/problems/sum-of-left-leaves/description/>

## Description

Find the sum of all left leaves in a given binary tree.

**Example**:
```
    3
   / \
  9  20
    /  \
   15   7
```
There are two left leaves in the binary tree, with values `9` and `15` respectively. Return `24`.

## Solution

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def sumOfLeftLeaves(self, root):
        """
        :type root: TreeNode
        :rtype: int
        """
        def f(r):
            if r==None:
                return 0
            ret_sum = 0
            if r.left:
                if r.left.left == None and r.left.right==None:
                    ret_sum += r.left.val
                else:
                    ret_sum += f(r.left)
            ret_sum += f(r.right)
            return ret_sum
        return f(root)
```
