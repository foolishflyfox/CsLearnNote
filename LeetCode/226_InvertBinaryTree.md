---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 226. Invert Binary Tree

> <https://leetcode.com/problems/invert-binary-tree/description/>

## Description

Invert a binary tree.

**Example**:

**Input**:
```
     4
   /   \
  2     7
 / \   / \
1   3 6   9
```

**Output**:
```
     4
   /   \
  7     2
 / \   / \
9   6 3   1
```

## Solution

```python
# Definition for a binary tree node.
class TreeNode:
    def __init__(self, x):
        self.val = x
        self.left = None
        self.right = None

class Solution:
    def invertTree(self, root):
        """
        :type root: TreeNode
        :rtype: TreeNode
        """
        if root==None:
            return None
        left_inverted = self.invertTree(root.left)
        right_inverted = self.invertTree(root.right)
        root.left = right_inverted
        rott.right = left_inverted
        return root
```

