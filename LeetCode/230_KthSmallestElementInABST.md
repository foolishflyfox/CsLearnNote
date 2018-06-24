---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 230. Kth Smallest Element in a BST

> <https://leetcode.com/problems/kth-smallest-element-in-a-bst/description/>

## Description

Given a binary search tree, write a function kthSmallest to find the `kth smallest` element in it.

**Note**: 
You may assume k is always valid, 1 ≤ k ≤ BST's total elements.

**Example 1**:
**Input**: `root = [3,1,4,null,2], k = 1`
```
   3
  / \
 1   4
  \
   2
```
**Output**: `1`

**Example 2**:
**Input**: `root = [5,3,6,2,4,null,null,1], k = 3`
```
       5
      / \
     3   6
    / \
   2   4
  /
 1
```
**Output**: 3

**Follow up**:
What if the BST is modified (insert/delete operations) often and you need to find the kth smallest frequently? How would you optimize the kthSmallest routine?

## Solution

### Solution-1

将二叉搜索树 (BST, Binary Search Tree) 通过一次遍历转换为一个值递增链表，然后取出第 k 小的值：

```python {class=line-numbers}
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Node:
    def __init__(self, x):
        self.val = x
        self.next = None

class Solution:
    def BST2chain(self, tree):
        if tree==None: return None, None
        node = Node(tree.val)
        head = tail = node
        left_head, left_tail = self.BST2chain(tree.left)
        right_head, right_tail = self.BST2chain(tree.right)
        if left_head:
            head = left_head
            left_tail.next = node
        if right_head:
            node.next = right_head
            tail = right_tail
        return head, tail
    def kthSmallest(self, root, k):
        """
        :type root: TreeNode
        :type k: int
        :rtype: int
        """
        head,tail = self.BST2chain(root)
        for i in range(k-1):
            head = head.next
        return head.val
```
该代码在 LeetCode 上的测试结果为：84 ms。

### Solution-2

用递归进行中序遍历，并通过计数器计算记录当前获取的是第几小的值：
```python {class=line-numbers}
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def get_ksmallest(self, tree):
        if tree==None:return None
        left_v = self.get_ksmallest(tree.left)
        if left_v!=None:return left_v
        self.count += 1
        if self.count == self.k:return tree.val
        right_v = self.get_ksmallest(tree.right)
        if right_v!=None:return right_v
        return None
    def kthSmallest(self, root, k):
        """
        :type root: TreeNode
        :type k: int
        :rtype: int
        """
        self.count = 0
        self.k = k
        return self.get_ksmallest(root)
```
该代码在 LeetCode 上的测试结果为：64 ms。



