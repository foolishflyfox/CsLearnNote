---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 82. Remove Duplicates from Sorted List II

> <https://leetcode.com/problems/remove-duplicates-from-sorted-list-ii/description/>

## Description

Given a sorted linked list, delete all nodes that have duplicate numbers, leaving only distinct numbers from the original list.

**Example 1**:
**Input**: `1->2->3->3->4->4->5`
**Output**: `1->2->5`

**Example 2**:
**Input**: `1->1->1->2->3`
**Output**: `2->3`

## Solution

```python{class=line-numbers}
# Definition for singly-linked list.
# class ListNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution(object):
    def deleteDuplicates(self, head):
        """
        :type head: ListNode
        :rtype: ListNode
        """
        ret = ListNode(0)
        p_ret = ret
        p_ori = head
        while p_ori:
            if p_ori.next and p_ori.next.val==p_ori.val:
                v = p_ori.val
                while p_ori and p_ori.val==v:
                    p_ori = p_ori.next
            else:
                p_ret.next = p_ori
                p_ori = p_ori.next
                p_ret = p_ret.next
        p_ret.next =None
        return ret.next
```
