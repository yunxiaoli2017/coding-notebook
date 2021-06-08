------

### 391. Perfect Rectangle
> Given N axis-aligned rectangles where N > 0, determine if they all together form an exact cover of a rectangular region.
> 
> Each rectangle is represented as a bottom-left point and a top-right point. For example, a unit square is represented as [1,1,2,2]. (coordinate of bottom-left point is (1, 1) and top-right point is (2, 2)).

Two conditions that needs meeting:
1. Total area == area of rectangle
2. All corners need to appear 2 or 4 times, except for the four corner of the large rectangle which should appear only once.

```python
class Solution(object):
    # Time O(n) Space O(n)
    def isRectangleCover(self, rectangles):
        corners = {} 
        L, B, R, T, area = float('inf'), float('inf'), float('-inf'), float('-inf'), 0
        for x1, y1, x2, y2 in rectangles:
            L, B, R, T = min(L, x1), min(B, y1), max(R, x2), max(T, y2)
            area += (x2 - x1) * (y2 - y1)
            for corner in [(x1, y1), (x1, y2), (x2, y1), (x2, y2)]:
                corners[corner] = corners.setdefault(corner, 0) + 1
        if area != (T - B) * (R - L):
            return False
        four_corner = [(L, B), (R, T), (L, T), (R, B)]
        for corner in four_corner:
            if corner not in corners or corners[corner] != 1:
                return False
        for corner in corners:
            if corners[corner] % 2 and corner not in four_corner:
                return False
        return True
```
