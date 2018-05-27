---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 222. Count Complete Tree Nodes

> <https://leetcode.com/problems/count-complete-tree-nodes/description/>

## Description

Given a **complete binary tree**, count the number of nodes.

**Note**:

[Definition of a complete binary tree from Wikipedia](https://en.wikipedia.org/wiki/Binary_tree#Types_of_binary_trees):
In a complete binary tree every level, except possibly the last, is completely filled, and all nodes in the last level are as far left as possible. It can have between 1 and 2<sup>h</sup> nodes inclusive at the last level h.

**Example**:

**Input**:
``` 
    1
   / \
  2   3
 / \  /
4  5 6
```
**Output**: 6


## Solution

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def countNodes(self, root):
        """
        :type root: TreeNode
        :rtype: int
        """
        def count_R(rt):
            if rt==None:
                return 0
            left_depth = right_depth = 0
            tree_l = tree_r = rt
            while tree_l:
                left_depth += 1
                tree_l = tree_l.left
            while tree_r:
                right_depth += 1
                tree_r = tree_r.right
            if right_depth==left_depth:
                return 2**right_depth-1
            return count_R(rt.left)+count_R(rt.right)+1
        return count_R(root)
```

