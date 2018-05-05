---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 328. Odd Even Linked List

> <https://leetcode.com/problems/odd-even-linked-list/description/>

## Description

Given a singly linked list, group all odd nodes together followed by the even nodes. Please note here we are talking about the node number and not the value in the nodes.

You should try to do it in place. The program should run in O(1) space complexity and O(nodes) time complexity.

**Example**:
- Given `1->2->3->4->5->NULL`,
- return `1->3->5->2->4->NULL`.

**Note**:
The relative order inside both the even and odd groups should remain as it was in the input.

The first node is considered odd, the second node even and so on ...

## Solution

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def oddEvenList(self, head):
        """
        :type head: ListNode
        :rtype: ListNode
        """
        if not head:
            return None
        odd_head = head
        even_head = head.next
        if even_head==None:
            return head
        origin_pt = even_head.next
        odd_pt = odd_head
        even_pt = even_head
        i = 1
        while origin_pt:
            if i%2==1:
                odd_pt.next = origin_pt
                odd_pt = origin_pt
            else:
                even_pt.next = origin_pt
                even_pt = origin_pt
            origin_pt = origin_pt.next
            i += 1
        odd_pt.next = even_head
        even_pt.next = None
        return odd_head
```
