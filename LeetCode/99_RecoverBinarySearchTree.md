---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 99. Recover Binary Search Tree

> <https://leetcode.com/problems/recover-binary-search-tree/description/>

## Description

Two elements of a binary search tree (BST) are swapped by mistake.

Recover the tree without changing its structure.

**Example 1**:
**Input**: `[1,3,null,null,2]`
```
   1
  /
 3
  \
   2
```
**Output**: `[3,1,null,null,2]`
```
   3
  /
 1
  \
   2
```

**Example 2**:
**Input**: `[3,1,4,null,null,2]`
```
  3
 / \
1   4
   /
  2
```
**Output**: `[2,1,4,null,null,3]`
```
  2
 / \
1   4
   /
  3
```

**Follow up**:
- A solution using O(n) space is pretty straight forward.
- Could you devise a constant space solution?

## Solution

```python {class=line-number}
# Definition for a binary tree node.
# class TreeNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution(object):
    def recoverTree(self, root):
        """
        :type root: TreeNode
        :rtype: void Do not return anything, modify root in-place instead.
        """
        if root==None: return
        record = []
        cur_node, stack = root, []
        while cur_node or len(stack):
            if cur_node:
                if cur_node.left:
                    stack.append(cur_node)
                    cur_node = cur_node.left
                else:
                    record.append(cur_node)
                    cur_node = cur_node.right
            else:
                record.append(stack.pop())
                if record[-1].right:
                    cur_node = record[-1].right
        wrong1 = wrong2 = None
        for i in range(1, len(record)):
            if wrong1==None and record[i-1].val>record[i].val:
                wrong1 = record[i-1]
            if wrong1 and record[i].val>wrong1.val:
                wrong2 = record[i-1]
                break
        else:
            wrong2 = record[-1]
        wrong1.val, wrong2.val = wrong2.val, wrong1.val
```
