------

### 148. Sort List
> Given the head of a linked list, return the list after sorting it in ascending order.
> 
> Follow up: Can you sort the linked list in O(nlogn) time and O(1) memory (i.e. constant space)?

O(1) space is not achievable via recursion. We are looking for an iterative sorting algorithm.

```python
class Segment:
    
    def __init__(self, size: int, head: ListNode, tail: ListNode) -> None:
        self.size = size
        self.head = head
        self.tail = tail
        
class Solution:
    
    def get_sorted_segment(self, head: ListNode) -> Segment:
        if head is None:
            return None
        size = 1
        tail = head
        while tail.next is not None and tail.next.val >= tail.val:
            tail = tail.next
            size += 1
        return Segment(size, head, tail)
    
    def merge(self, seg1: Segment, seg2: Segment) -> Segment:
        if seg2 is None:
            return seg1
        if seg1 is None:
            return seg2
        size1, size2 = seg1.size, seg2.size
        node1, node2 = seg1.head, seg2.head
        dummy = ListNode()
        tail = dummy
        while size1 > 0 and size2 > 0:
            if node1.val <= node2.val:
                tail.next = node1
                node1 = node1.next
                size1 -= 1
            else:
                tail.next = node2
                node2 = node2.next
                size2 -= 1
            tail = tail.next
        if size1 > 0:
            tail.next = node1
            tail = seg1.tail
        elif size2 > 0:
            tail.next = node2
            tail = seg2.tail
        tail.next = None
        return Segment(seg1.size + seg2.size, dummy.next, tail)
    
    def sortList(self, head: ListNode) -> ListNode:
        if head is None:
            return None
        num_merge = 2
        while num_merge > 1:
            nextHead = head
            head = None
            lastTail = None
            num_merge = 0
            while nextHead is not None:
                seg1 = self.get_sorted_segment(nextHead);
                seg2 = self.get_sorted_segment(seg1.tail.next);
                if seg2 is not None:
                    nextHead = seg2.tail.next
                else:
                    nextHead = None
                merged = self.merge(seg1, seg2)
                if lastTail is not None:
                    lastTail.next = merged.head
                lastTail = merged.tail
                if head is None:
                    head = merged.head
                num_merge += 1
        return head
```
