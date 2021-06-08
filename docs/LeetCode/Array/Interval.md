------

### 56. Merge Intervals
> Given an array of intervals where intervals[i] = [starti, endi], merge all overlapping intervals, and return an array of the non-overlapping intervals that cover all the intervals in the input.

Sort by starti, and then go over it and keep concatenating until next's starti > cur's endi

<!-- tabs:start -->

#### **Java**

```java
public int[][] merge(int[][] intervals) {
  if (intervals.length == 0) return new int[][] {};
  Arrays.sort(intervals, (a, b) -> Integer.compare(a[0], b[0]));
  List<int[]> merged = new ArrayList<>();
  for (int[] interval : intervals) {
    if (merged.isEmpty() || interval[0] > merged.get(merged.size() - 1)[1]) {
      merged.add(interval);
    } else {
      merged.get(merged.size() - 1)[1] = Math.max(merged.get(merged.size() - 1)[1], interval[1]);
    }
  }
  return merged.toArray(new int[merged.size()][2]);
}
```

#### **Python**

```python
def merge(self, intervals: List[List[int]]) -> List[List[int]]:
    """Merges a list of intervals to have no overlapping."""
    if not intervals:
      return []
    intervals.sort(key=lambda interval: interval[0])
    merged = []
    cur_start, cur_end = intervals[0]
    for i in range(1, len(intervals)):
        interval = intervals[i]
        if interval[0] <= cur_end:
          cur_end = max(cur_end, interval[1])
        else:
          merged.append([cur_start, cur_end])
          cur_start, cur_end = interval
    merged.append([cur_start, cur_end])
    return merged
```

#### **C++**

```csharp
public:
  vector<vector<int>> merge(vector<vector<int>>& intervals) {
    if (intervals.size() == 0) {
      return intervals;
    }
    sort(intervals.begin(), intervals.end());
    vector<vector<int>> merged;
    for (auto interval : intervals) {
      if (merged.empty() || interval[0] > merged.back()[1]) {
        merged.push_back(interval);
      } else {
        merged.back()[1] = max(merged.back()[1], interval[1]);
      }
    }
    return merged;
  }
```

<!-- tabs:end -->


### 763. Partition Labels
> A string S of lowercase English letters is given. We want to partition this string into as many parts as possible so that each letter appears in at most one part, and return a list of integers representing the size of these parts.

1. One way to solve it is greedy. Continuously update right bound of current part by looking at the last occurence of current character.
2. The other way, however, views this problem exactly as ```56. Merge Intervals```. We get an interval [first, last] for all characters, and merge these intervals.

```python
class Solution:
    # Time O(n) Space O(26) Greedy
    def partitionLabels(self, S: str) -> List[int]:
        ord_a = ord('a')
        last_occur = [-1 for i in range(26)]
        for i, c in enumerate(S):
            last_occur[ord(c)-ord_a] = i
        cur_start, cur_end = 0, last_occur[ord(S[0])-ord_a]
        sizes = []
        i = 0
        while i < len(S):
            if i < cur_end:
                cur_end = max(cur_end, last_occur[ord(S[i])-ord_a])
            else:
                sizes.append(cur_end - cur_start + 1)
                cur_start = cur_end + 1
                if cur_start >= len(S):
                    break
                cur_end = last_occur[ord(S[cur_start])-ord_a]
            i += 1
        return sizes
```

### 986. Interval List Intersections
> Given two lists of closed intervals, each list of intervals is **pairwise disjoint** and in sorted order.
> 
> Return the intersection of these two interval lists.

The intervals in each list are **pairwise disjoint**. 
So each time we dealt with two lists, we need to increment the one with smaller right bound.

### 1229. Meeting Scheduler
> Given the availability time slots arrays slots1 and slots2 of two people and a meeting duration duration, return the earliest time slot that works for both of them and is of duration duration.
> 
> If there is no common time slot that satisfies the requirements, return an empty array.
> 
> The format of a time slot is an array of two elements [start, end] representing an inclusive time range from start to end.  
> 
> It is guaranteed that no two availability slots of the same person intersect with each other. 

First we need to sort two arrats by start times.   
Then each time after we considered two slots, we need to increment the one with smaller right bound.


### 57. Insert Interval
> Given a set of non-overlapping intervals, insert a new interval into the intervals (merge if necessary).
> 
> You may assume that the intervals were initially sorted according to their start times.

Append intervals that do not intersect with new_interval as they are.   
Merge intervals that do intersect with new_interval and new_interval as one single interval [minLeft, maxRight]

```python
class Solution:
    ### 57. Insert Interval
    def insert(self, intervals: List[List[int]], newInterval: List[int]) -> List[List[int]]:
        res = []
        i = 0
        while i < len(intervals) and intervals[i][1] < newInterval[0]:
            res.append(intervals[i])
            i += 1
        if i == len(intervals):
            res.append(newInterval)
            return res
        left = min(intervals[i][0], newInterval[0])
        right = newInterval[1]
        while i < len(intervals) and intervals[i][0] <= newInterval[1]:
            right = max(intervals[i][1], right)
            i += 1
        res.append([left, right])
        while i < len(intervals):
            res.append(intervals[i])
            i += 1
        return res
```

### 252. Meeting Rooms (Easy)
> Given an array of meeting time intervals where intervals[i] = [starti, endi], determine if a person could attend all meetings.

```python
    intervals.sort(key=lambda x: x[0])
```

### 253. Meeting Rooms II
> Given an array of meeting time intervals consisting of start and end times [[s1,e1],[s2,e2],...] (si < ei), find the minimum number of conference rooms required.

The key question is, when we get a meeting, which existing room should we arrange this meeting to?   
Answer: the room that ends earliest, if that room is available. If not, then we need a new room.

PriorityQueue is the data structure to handle this.  

```python
class Solution:
    ### 253. Meeting Rooms II
    def minMeetingRooms(self, intervals: List[List[int]]) -> int:
        heap = []
        heapq.heapify(heap)
        intervals.sort(key=lambda x: x[0])
        for interval in intervals:
            if len(heap) > 0 and interval[0] >= heap[0]:
                heapq.heappushpop(heap, interval[1])
            else:
                heapq.heappush(heap, interval[1])
        return len(heap)
```

### 715. Range Module (hard!)
> A Range Module is a module that tracks ranges of numbers. Your task is to design and implement the following interfaces in an efficient manner.
> 
> addRange(int left, int right) Adds the half-open interval [left, right), tracking every real number in that interval. Adding an interval that partially overlaps with currently tracked numbers should add any numbers in the interval [left, right) that are not already tracked.
> queryRange(int left, int right) Returns true if and only if every real number in the interval [left, right) is currently being tracked.
> removeRange(int left, int right) Stops tracking every real number currently being tracked in the interval [left, right).

The key operation is find all intervals that can possibly intersect with target interval.   
This can be done with binary search, if the intervals list is maintained in sorted order without overlapping.   
We need to binary search for    
- The first interval that has ```right > target_left```      
- The last interval that has ```left < target_right```

All operations only need to consider these two intervals.

```python
class RangeModule:

    def __init__(self):
        self.ranges = []
    
    # Time O(n)
    def addRange(self, left: int, right: int) -> None:
        if not self.ranges:
            self.ranges.append([left, right])
        i, j = self._bounds(left, right)
        if i <= j:
            left = min(left, self.ranges[i][0])
            right = max(right, self.ranges[j][1])
        self.ranges[i:j+1] = [(left, right)]
    
    # Time O(logn)
    def queryRange(self, left: int, right: int) -> bool:
        if not self.ranges:
            return False
        i = self._binary_search_first_intersect(left)
        if i >= len(self.ranges):
            return False
        return self.ranges[i][0] <= left and self.ranges[i][1] >= right
    
    # Time O(n)
    def removeRange(self, left: int, right: int) -> None:
        if not self.ranges:
            return
        i, j = self._bounds(left, right)
        if i > j:
            return
        replace = []
        if self.ranges[i][0] < left:
            replace.append((self.ranges[i][0], left))
        if self.ranges[j][1] > right:
            replace.append((right, self.ranges[j][1]))
        self.ranges[i:j+1] = replace
        
    def _bounds(self, left: int, right: int) -> tuple[int, int]:
        """return indices of first and last ranges that possibly intersect with (left, right)"""
        return (self._binary_search_first_intersect(left), 
                self._binary_search_last_intersect(right))
    
    def _binary_search_first_intersect(self, left: int) -> int:
        """binary search for first range [x, y) with y >= left"""
        lo, hi = 0, len(self.ranges) - 1
        while lo < hi:
            mid = lo + (hi - lo) // 2
            if self.ranges[mid][1] >= left:
                hi = mid
            else:
                lo = mid + 1
        if self.ranges[lo][1] >= left:
            return lo
        return len(self.ranges)
                
    def _binary_search_last_intersect(self, right: int) -> int:
        """binary search for last range [x, y) with x <= right"""
        lo, hi = 0, len(self.ranges) - 1
        while lo < hi:
            mid = hi - (hi - lo) // 2
            if self.ranges[mid][0] > right:
                hi = mid - 1
            else:
                lo = mid
        if self.ranges[lo][0] <= right:
            return lo
        return -1
        
```

### 759. Employee Free Time
> We are given a list schedule of employees, which represents the working time for each employee.
> 
> Each employee has a list of non-overlapping Intervals, and these intervals are in sorted order.
> 
> Return the list of finite intervals representing common, positive-length free time for all employees, also in sorted order.

Imagine this scenario: Once a person starts working, we all need to wait until he finishes. If any other person starts working before he finishes, then we need to wait for both of them;    
The sign of that we are all free, is that **the next earliest starting time** is later than current end that we need to wait until.   
This makes it a PriorityQueue problem.   

```python
class Solution:
    # Time O(nlogk) n is total number of intervals; k is number of employees
    # Space O(k)
    def employeeFreeTime(self, schedule: '[[Interval]]') -> '[Interval]':
        """given employees' schedules, get free intervals that work for all"""
        free_time = []
        heap = []
        count = 0
        for intervals in schedule:
            if intervals:
                heap.append((intervals[0].start, count, intervals, 0))
                count += 1
        heapq.heapify(heap)
        cur_end = float('inf')
        while heap:
            *_, intervals, index = heapq.heappop(heap)
            interval = intervals[index]
            if cur_end == float('inf'):
                cur_end = interval.end
            elif interval.start <= cur_end:
                cur_end = max(cur_end, interval.end)
            else:
                free_time.append(Interval(cur_end, interval.start))
                cur_end = interval.end
            if index + 1 < len(intervals):
                next_interval = intervals[index + 1]
                heapq.heappush(heap, (next_interval.start, 
                                      count, intervals, index + 1))
                count += 1
        return free_time
  ```
