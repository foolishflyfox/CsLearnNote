---
export_on_save:
 html: ture
---

@import "/blog_head.md"

# 95. Unique Binary Search Trees II

## Description

Given an integer n, generate all structurally unique **BST's** (binary search trees) that store values 1 ... n.

**Example**:
> **Input**: 3
**Output**:
```
[
    [1,null,3,2],
    [3,2,null,1],
    [3,1,null,null,2],[2,1,3],
    [1,null,2,null,3]
]
```
**Explanation**:
The above output corresponds to the 5 unique BST's shown below:
```
   1         3     3      2      1
    \       /     /      / \      \
     3     2     1      1   3      2
    /     /       \                 \
   2     1         2                 3
```
## Solution

使用递归的方式构建BST：
```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def generate_tree(self, a):
        if len(a)==0:return [None]
        if len(a)==1:return [TreeNode(a[0])]
        ret_list = []
        for i in range(len(a)):
            lefts = self.generate_tree(a[:i])
            rights = self.generate_tree(a[i+1:])
            for left in lefts:
                for right in rights:
                    root = TreeNode(a[i])
                    root.left = left
                    root.right = right
                    ret_list.append(root)
        return ret_list
                    
    def generateTrees(self, n):
        """
        :type n: int
        :rtype: List[TreeNode]
        """
        if n<1:return []
        return self.generate_tree(range(1, n+1))
```


