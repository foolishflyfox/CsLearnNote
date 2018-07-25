---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 25. Reverse Nodes in k-Group

> <https://leetcode.com/problems/reverse-nodes-in-k-group/description/>

## Description

Given a linked list, reverse the nodes of a linked list k at a time and return its modified list.

k is a positive integer and is less than or equal to the length of the linked list. If the number of nodes is not a multiple of k then left-out nodes in the end should remain as it is.

**Example**:

Given this linked list: `1->2->3->4->5`

For k = 2, you should return: `2->1->4->3->5`

For k = 3, you should return: `3->2->1->4->5`

**Note**:

- Only constant extra memory is allowed.
- You may not alter the values in the list's nodes, only nodes itself may be changed.

## Solution

```python {class=line-numbers}
# Definition for singly-linked list.
# class ListNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution(object):
    def reverseK(self, head, k):
        p = head
        i = 0
        for i in range(k-1):
            if p.next == None:
                self.head = None
                return head, p
            p = p.next
        h1 = head
        h2 = head.next
        for i in range(k-1):
            tp = h2
            h2 = h2.next
            tp.next = h1
            h1 = tp
        self.head = h2
        return h1, head
    def reverseKGroup(self, head, k):
        """
        :type head: ListNode
        :type k: int
        :rtype: ListNode
        """
        if k<=1: return head
        self.head = head
        pre_head = None
        ret = None
        while self.head:
            tp_head, tp_tail = self.reverseK(self.head, k)
            if ret==None:
                ret = tp_head
            else:
                pre_tail.next = tp_head
            pre_tail = tp_tail
            tp_tail.next = None
        return ret
```

