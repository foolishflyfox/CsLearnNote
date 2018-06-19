---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 100. Same Tree

> <https://leetcode.com/problems/same-tree/description/>

## Description

Given two binary trees, write a function to check if they are the same or not.

Two binary trees are considered the same if they are structurally identical and the nodes have the same value.

**Example 1**:

**Input**:
```
           1         1
          / \       / \
         2   3     2   3

        [1,2,3],   [1,2,3]
```
**Output**: `true`

---

**Example 2**:
**Input**:
```
           1         1
          /           \
         2             2

        [1,2],     [1,null,2]
```
**Output**: `false`

---

**Example 3**:

**Input**:
```
           1         1
          / \       / \
         2   1     1   2

        [1,2,1],   [1,1,2]
```
**Output**: `false`


## Solution

### Solution-1

通过宽度优先的遍历（或者说前序遍历）比较两棵树是否相同：
```python {class=line-numbers}
class Solution(object):
    def get_midview(self, tree):
        if tree==None:return []
        return (self.get_midview(tree.left)v+[tree.val] +
            self.get_midview(tree.right))
    def isSameTree(self, p, q):
        """
        :type p: TreeNode
        :type q: TreeNode
        :rtype: bool
        """
        if p==None and q==None:return True
        if p==None or q==None:return False
        if p.val!=q.val:return False
        p_nodes = [p]
        q_nodes = [q]
        while len(p_nodes) or len(q_nodes):
            if len(p_nodes)!=len(q_nodes):return False
            pn = p_nodes.pop()
            qn = q_nodes.pop()
            if pn.right==None:
                if qn.right!=None:return False
            else:
                if qn.right==None:return False
                if qn.right.val!=pn.right.val:return False
                p_nodes.append(pn.right)
                q_nodes.append(qn.right)
            if pn.left==None:
                if qn.left!=None:return False
            else:
                if qn.left==None:return False
                if qn.left.val!=pn.left.val:return False
                p_nodes.append(pn.left)
                q_nodes.append(qn.left)
        return True
```
缺点：逻辑稍微看起来有点复杂。

### Solution-2
求出其前序遍历和中序遍历的结果，比较两个结果是否相等：
```python {class=line-numbers}
class Solution(object):
    def get_midview(self, tree):
        if tree==None:return []
        return self.get_midview(tree.left)+[tree.val]+self.get_midview(tree.right)
    def get_preview(self, tree):
        if tree==None:return []
        return [tree.val]+self.get_preview(tree.left)+self.get_preview(tree.right)
    def isSameTree(self, p, q):
        """
        :type p: TreeNode
        :type q: TreeNode
        :rtype: bool
        """
        p_midview = self.get_midview(p)
        q_midview = self.get_midview(q)
        if len(p_midview)!=len(q_midview):return False
        for i,j in zip(p_midview, q_midview):
            if i!=j:
                return False
        p_preview = self.get_preview(p)
        q_preview = self.get_preview(q)
        for i,j in zip(p_preview, q_preview):
            if i!=j:
                return False
        return True
```
结果：逻辑错误，如下面的树：
```
           1         1
          /           \
         1             1

        [1,1],     [1,null,1]
```
前序遍历和中序遍历的结果都是[1,1]，但两个树不相同。

总结：
- 只有在节点不同时，前序遍历和中序遍历才能唯一确定一棵树；

### Solution-3

写出其前序遍历，左子节点为空且右子节点不为空时填充 Null，如
```
           1         1
          /           \
         1             1

        [1,1],     [1,null,1]
```
然后比较两个遍历数组：
```python {class=line-numbers}
class Solution(object):
    def get_preview(self, tree):
        if tree==None:return []
        if tree.left:
            return [tree.val]+self.get_preview(tree.left)+self.get_preview(tree.right)
        else:
            if tree.right==None:
                return [tree.val]
            else:
                return [tree.val]+[None]+self.get_preview(tree.right)
    def isSameTree(self, p, q):
        """
        :type p: TreeNode
        :type q: TreeNode
        :rtype: bool
        """
        p_preview = self.get_preview(p)
        q_preview = self.get_preview(q)
        if len(p_preview)!=len(q_preview):
            return False
        for i,j in zip(p_preview, q_preview):
            if i!=j:
                return False
        return True
```
**注意**：`for i,j in zip(list1, list2)` 不相等也能进行，取短的数组长度迭代，如代码：
```python
for i,j in zip([1,2,3],[10,20]):
    print(i,j)
```
输出为：
```
1 10
2 20
```
### Solution-4

解决方案 Solution-3 的逻辑还不是非常简单，树的很多操作都可以通过递归进行，故下面使用递归算法解决该问题：
```python {class=line-numbers}
class Solution(object):
    def isSameTree(self, p, q):
        """
        :type p: TreeNode
        :type q: TreeNode
        :rtype: bool
        """
        if p==None and q==None:return True
        if p==None or q==None:return False
        if p.val!=q.val:return False
        return (self.isSameTree(p.left, q.left) and 
               self.isSameTree(p.right, q.right))
```
该代码非常简单，效率也非常高。

### Solution-5

通常来说，递归调用的空间复杂度会较高，因此下面将 Solution-4 的递归转换为 `for` 迭代的算法：
```python{class=line-numbers}
class Solution(object):
    def isSameTree(self, p, q):
        """
        :type p: TreeNode
        :type q: TreeNode
        :rtype: bool
        """
        pstack = [p]
        qstack = [q]
        while len(pstack) or len(qstack):
            pnode = pstack.pop()
            qnode = qstack.pop()
            if pnode==qnode==None:continue
            if pnode==None or qnode==None:return False
            if pnode.val!=qnode.val:return False
            pstack += [pnode.right, pnode.left]
            qstack += [qnode.right, qnode.left]
        return True
```


