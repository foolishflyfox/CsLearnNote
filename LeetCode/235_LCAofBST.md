---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 235. Lowest Common Ancestor of a Binary Search Tree 

> <https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/description/>

## Description

Given a binary search tree (BST), find the lowest common ancestor (LCA) of two given nodes in the BST.

According to the definition of LCA on Wikipedia: “The lowest common ancestor is defined between two nodes p and q as the lowest node in T that has both p and q as descendants (where we allow **a node to be a descendant of itself**).”

Given binary search tree:  `root = [6,2,8,0,4,7,9,null,null,3,5]`
```
        _______6______
       /              \
    ___2__          ___8__
   /      \        /      \
   0      _4       7       9
         /  \
         3   5
```
**Example 1**:
**Input**: `root = [6,2,8,0,4,7,9,null,null,3,5], p = 2, q = 8`
**Output**: `6`
**Explanation**: The LCA of nodes 2 and 8 is 6.

**Example 2**:
**Input**: `root = [6,2,8,0,4,7,9,null,null,3,5], p = 2, q = 4`
**Output**: `2`
**Explanation**: The LCA of nodes 2 and 4 is 2, since a node can be a descendant of itself according to the LCA definition.

## Solution

### Solution-1

建立双向二叉树（可以根据子节点找到父节点），之后寻找共同的父节点：
```python {class=line-numbers}
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def lowestCommonAncestor(self, root, p, q):
        """
        :type root: TreeNode
        :type p: TreeNode
        :type q: TreeNode
        :rtype: TreeNode
        """
        # 双向二叉树节点定义
        class BiNode:
            def __init__(self, x, parent):
                self.val = x
                self.parent = parent
                self.left = None
                self.right = None
                self.origin_node = None
        # 递归创建双向二叉树
        def create_bitree(tree, parent):
            if tree==None: return None
            nonlocal bi_p, bi_q
            binode = BiNode(tree.val, parent)
            binode.left = create_bitree(tree.left, binode)
            binode.right = create_bitree(tree.right, binode)
            binode.origin_node = tree
            if tree.val==p.val:
                bi_p = binode
            if tree.val==q.val:
                bi_q = binode
            return binode
        bi_p = None
        bi_q = None
        create_bitree(root, None)
        # 寻找共同的父节点
        p_chain = []
        q_chain = []
        while bi_p:
            p_chain.append(bi_p)
            bi_p = bi_p.parent
        while bi_q:
            q_chain.append(bi_q)
            bi_q = bi_q.parent
        ret = None
        for n1, n2 in zip(p_chain[::-1], q_chain[::-1]):
            if n1.val==n2.val:
                ret = n1
            else:
                break
        return ret
```
上述代码在 LeetCode 上的运行时间为：184 ms；

### Solution-2

上述的代码还需要建立一棵双向二叉树，比较麻烦。我们想到，完全二叉树的静态表示时：id=1 表示根节点，id=2 表示根节点的左子节点，...

二叉树：
```
        _______6______
       /              \
    ___2__          ___8__
   /      \        /      \
   0      _4       7       9
         /  \
         3   5
```
的 id 为：
|节点值|id号|
|---|---|
|6|1|
|2|2|
|8|3|
|0|4|
|4|5|
|7|6|
|9|7|
|3|10|
|5|11|

即一个节点的 id=x，则其父节点 id 为 $int(x/2)$ ，其左子节点的 id 为 $2x$ ，右子节点的 id 为 $2x+1$，我们只要建立每个节点的 id 号，就很容易找到其父、子节点。代码如下：
```python {class=line-numbers}
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def map_node(self, node, id_num):
        if node==None:return
        self.value2id[node.val] = (id_num, node)
        self.id2value[id_num] = node.val
        self.map_node(node.left, 2*id_num)
        self.map_node(node.right, 2*id_num+1)
    def lowestCommonAncestor(self, root, p, q):
        """
        :type root: TreeNode
        :type p: TreeNode
        :type q: TreeNode
        :rtype: TreeNode
        """
        self.value2id = dict()
        self.id2value = dict()
        self.map_node(root, 1)
        p_id = self.value2id[p.val][0]
        q_id = self.value2id[q.val][0]
        p_parents = []
        q_parents = []
        while p_id:
            p_parents.append(p_id)
            p_id //= 2
        while q_id:
            q_parents.append(q_id)
            q_id //= 2
        common_id = -1
        for i,j in zip(p_parents[::-1], q_parents[::-1]):
            if i==j:
                common_id = i
        return self.value2id[self.id2value[common_id]][1]
```
代码长度减少了一些，并且在 LeetCode 上测试的运行时间为 132ms。
