------

### 850. Rectangle Area II
> We are given a list of (axis-aligned) rectangles. Each rectangle[i] = [xi1, yi1, xi2, yi2] , where (xi1, yi1) are the coordinates of the bottom-left corner, and (xi2, yi2) are the coordinates of the top-right corner of the ith rectangle.
> 
> Find the total area covered by all rectangles in the plane. Since the answer may be too large, return it modulo 10^9 + 7.

Imagine we have a horizontal line sweeping from bottom to top.   
At each height, we count the total covered horizontal length. We can maintain a list of intervals. Getting bottom edge of a rectangle means adding a interval; While reaching top edge of a rectangle means removing a interval (and that interval must be present since it was added before).   
We update the area when height increases.   

```python
class Solution:
    _OPEN = 1
    _CLOSE = 0
    _MODULO = 1000000007 # 10^9 + 7
    # Time O(n^2)
    def rectangleArea(self, rectangles: List[List[int]]) -> int:
        events = []
        for x1, y1, x2, y2 in rectangles:
            events.append((y1, self._OPEN, x1, x2))
            events.append((y2, self._CLOSE, x1, x2))
        events.sort()
        cur_y, area, active = 0, 0, []
        for y, state, x1, x2 in events:
            if y > cur_y:
                area += self.query(active) * (y - cur_y)
                area %= self._MODULO
                cur_y = y
            if state == self._OPEN:
                self.insert(active, (x1, x2))
            else:
                active.remove((x1, x2))
        return area
            
    def query(self, intervals: List[tuple[int, int]]) -> int:
        """return total covered length of a list of intervals"""
        cur_left, cur_right = 0, 0
        covered_length = 0
        for x1, x2 in intervals:
            if x1 > cur_right:
                covered_length += cur_right - cur_left
                cur_left, cur_right = x1, x2
            else:
                cur_right = max(cur_right, x2)
        covered_length += cur_right - cur_left
        return covered_length
    
    def insert(self, 
               intervals: List[tuple[int, int]], 
               interval: tuple[int, int]) -> None:
        lo, hi = 0, len(intervals) - 1
        while lo < hi:
            mid = lo + (hi - lo) // 2
            if intervals[mid][0] > interval[0]:
                hi = mid
            else:
                lo = mid + 1
        if not intervals or intervals[lo][0] <= interval[0]:
            intervals.append(interval)
        else:
            intervals.insert(lo, interval)
```

### 218. The Skyline Problem
> A city's skyline is the outer contour of the silhouette formed by all the buildings in that city when viewed from a distance. Given the locations and heights of all the buildings, return the skyline formed by these buildings collectively.
> 
> The geometric information of each building is given in the array buildings where buildings[i] = [lefti, righti, heighti]:
> 
> lefti is the x coordinate of the left edge of the ith building.
> righti is the x coordinate of the right edge of the ith building.
> heighti is the height of the ith building.
> You may assume all buildings are perfect rectangles grounded on an absolutely flat surface at height 0.
> 
> The skyline should be represented as a list of "key points" sorted by their x-coordinate in the form [[x1,y1],[x2,y2],...]. Each key point is the left endpoint of some horizontal segment in the skyline except the last point in the list, which always has a y-coordinate 0 and is used to mark the skyline's termination where the rightmost building ends. Any ground between the leftmost and rightmost buildings should be part of the skyline's contour.
> 
> Note: There must be no consecutive horizontal lines of equal height in the output skyline. For instance, [...,[2 3],[4 5],[7 5],[11 5],[12 7],...] is not acceptable; the three lines of height 5 should be merged into one in the final output as such: [...,[2 3],[4 5],[12 7],...]

<img src="./LeetCode/Images/218.png" alt="218. The Skyline Problem" height="300"/>

```python
from sortedcontainers import SortedDict
class Solution:
    _LEFT = 1
    _RIGHT = 0
    # Time O(nlogn)
    def getSkyline(self, buildings: List[List[int]]) -> List[List[int]]:
        events = []
        for left, right, height in buildings:
            events.append((left, self._LEFT, height))
            events.append((right, self._RIGHT, height))
        events.sort() # O(nlogn)
        active = SortedDict([])
        cur_x, last_height = 0, 0
        res = []
        for x, state, y in events: # O(n)
            if x > cur_x:
                height = active.peekitem()[0] if active else 0 # O(logn)
                last_height = res[-1][1] if res else 0
                if height != last_height:
                    res.append([cur_x, height])
                cur_x = x
            if state == self._LEFT:
                active.update([(y, active.setdefault(y, 0) + 1)]) # O(logn)
            else:
                if active.get(y) == 1:
                    active.pop(y) # O(logn)
                else:
                    active.update([(y, active.get(y) - 1)]) # O(logn)
        if res and res[-1][1] != 0:
            res.append([x, 0])
        return res
```
