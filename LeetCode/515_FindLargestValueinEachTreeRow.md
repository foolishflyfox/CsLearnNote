---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 515. Find Largest Value in Each Tree Row

> <https://leetcode.com/problems/find-largest-value-in-each-tree-row/description/>

## Description

You need to find the largest value in each row of a binary tree.

**Example**:
**Input**: 
```
          1
         / \
        3   2
       / \   \  
      5   3   9 
```
**Output**: `[1, 3, 9]`

## Solution

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def largestValues(self, root):
        """
        :type root: TreeNode
        :rtype: List[int]
        """
        if root==None:return []
        ret = []
        one_layer = [root]
        while len(one_layer):
            max_v = one_layer[0].val
            tp_layer = []
            for node in one_layer:
                if node.val > max_v:
                    max_v = node.val
                if node.left:
                    tp_layer.append(node.left)
                if node.right:
                    tp_layer.append(node.right)
            one_layer = tp_layer
            ret.append(max_v)
        return ret
```





