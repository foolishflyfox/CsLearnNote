---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 94. Binary Tree Inorder Traversal

> <https://leetcode.com/problems/binary-tree-inorder-traversal/description/>

## Description

Given a binary tree, return the inorder traversal of its nodes' values.

**Example**:

**Input**: 
```
[1,null,2,3]

   1
    \
     2
    /
   3
```
**Output**: `[1,3,2]`

**Follow up**: Recursive solution is trivial, could you do it iteratively?

## Solution

```python {class=line-numbers}
# Definition for a binary tree node.
# class TreeNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution(object):
    def inorderTraversal(self, root):
        """
        :type root: TreeNode
        :rtype: List[int]
        """
        if root==None: return []
        visited = set()
        container = [root]
        ret = []
        while container:
            if container[-1].left==None or id(container[-1].left) in visited:
                node = container.pop()
                ret.append(node.val)
                visited.add(id(node))
                if node.right:
                    container.append(node.right)
            else:
                container.append(container[-1].left)
        return ret
```

