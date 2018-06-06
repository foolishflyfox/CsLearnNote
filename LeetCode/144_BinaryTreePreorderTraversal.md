---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 144. Binary Tree Preorder Traversal

> <https://leetcode.com/problems/binary-tree-preorder-traversal/description/>

## Description

Given a binary tree, return the preorder traversal of its nodes' values.

**Example**:

**Input:** `[1,null,2,3]`
```
   1
    \
     2
    /
   3
```
**Output:** `[1,2,3]`

**Follow up:** Recursive solution is trivial, could you do it iteratively?

## Solution

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def preorderTraversal(self, root):
        """
        :type root: TreeNode
        :rtype: List[int]
        """
        from collections import deque
        ret = []
        q = deque([root])
        while len(q):
            cur_node = q.popleft()
            if cur_node != None:
                ret.append(cur_node.val)
                q.appendleft(cur_node.right)
                q.appendleft(cur_node.left)
        return ret
```
