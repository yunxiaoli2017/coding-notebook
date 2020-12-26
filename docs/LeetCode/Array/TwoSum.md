------
Note: Possible outputs are bool, one valid answer, all valid answers, all valid answers without duplicate combination
### 1. Two Sum
Use hashtable. Time O(n) Space O(n)

### 170. Two Sum III - Data structure design
> Implement the TwoSum class:
> 
> TwoSum() Initializes the TwoSum object, with an empty array initially.
> void add(int number) Adds number to the data structure.
> boolean find(int value) Returns true if there exists any pair of numbers whose sum is equal to value, otherwise, it returns false.  

Use hashtable. Overall Space O(n); void add(int number) Time O(1); boolean find(int value) Time O(n)

### 167. Two Sum II - Input array is sorted
1. Use hashtable. Time O(n^2) Space O(n)
2. Use binary search naively. Time O(nlogn) Space O(1)
3. Use two pointers. Time O(n) Space O(1)
4. Use two pointers and binary search wisely. Time **better than O(n)** Space O(1)

### 1099. Two Sum Less Than K
Sort the list; Then apply 3rd methods in #167. Time O(nlogn) **Space O(n)** (Python's sort is [**Timsort**](https://en.wikipedia.org/wiki/Timsort), a combination of mergesort and insertion sort; A choice made for ensuring stability)

```python
# 167. Two Sum II - Input array is sorted
def twoSum(self, numbers: List[int], target: int) -> List[int]:
    lo, hi = 0, len(numbers) - 1
    while lo < hi:
        cur_sum = numbers[lo] + numbers[hi]
        mid = lo + (hi - lo) // 2
        if cur_sum < target:
            if numbers[mid] < target - numbers[hi]:
                lo = mid + 1
            else:
                lo += 1
        elif cur_sum > target:
            if numbers[mid] > target - numbers[lo]:
                hi = mid - 1
            else:
                hi -= 1
        else:
            return [lo + 1, hi + 1]
    return [-1, -1]
```

### 653. Two Sum IV - Input is a BST
1. Do an inorder traversal and construct a sorted list. Then use 167. Time O(n) Space O(n)
2. Build two iterators, inorder and reverse-order, simulating two pointers. Time O(n) **Space O(h)**

### 1214. Two Sum BSTs
> Given two binary search trees, return True if and only if there is a node in the first tree and a node in the second tree whose values sum up to a given integer target.
Still two iterators, one for each binary tree. Continue if there are nodes untraversed in both trees.

```python
# 653. Two Sum IV - Input is a BST
class BinaryTreeIterator:
    # Space O(h), h is height of the tree
    def __init__(self, root: TreeNode, reverse=False):
        self.stack = []
        self.reverse = reverse
        node = root
        while node:
            self.stack.append(node)
            node = node.right if self.reverse else node.left
        
    def has_next(self) -> bool:
        return len(self.stack) > 0
    
    # Time O(1) amortized
    def next(self) -> int:
        if not self.has_next():
            return None
        node = self.stack.pop()
        val = node.val
        node = node.left if self.reverse else node.right
        while node:
            self.stack.append(node)
            node = node.right if self.reverse else node.left
        return val

class Solution:
    # Have two iterators, simulating two pointers, inorder and reverse-order.
    # Time O(n) Space O(h)
    def findTarget(self, root: TreeNode, target: int) -> bool:
        inorder_iterator = BinaryTreeIterator(root)
        reverse_iterator = BinaryTreeIterator(root, reverse=True)
        lo, hi = inorder_iterator.next(), reverse_iterator.next()
        while lo < hi:
            cur_sum = lo + hi
            if cur_sum < target:
                lo = inorder_iterator.next()
            elif cur_sum > target:
                hi = reverse_iterator.next()
            else:
                return True
        return False
```

### 15. 3Sum
### 16. 3Sum Closest
> Given an array nums of n integers and an integer target, find three integers in nums such that the sum is closest to target. Return the sum of the three integers. You may assume that each input would have exactly one solution.

Use two pointers, think carefully about **early-termination** condition!
### 259. 3Sum Smaller
> Given an array of n integers nums and an integer target, find the number of index triplets i, j, k with 0 <= i < j < k < n that satisfy the condition nums[i] + nums[j] + nums[k] < target.

Use two pointers, if nums[lo] + nums[hi] < target, then so do (lo, lo + 1), (lo, lo + 2), ..., (lo, hi - 1). In total, there would be **hi - lo** numbers of pairs that start with lo, and sum < target.
```python
def twoSum(self, nums: List[int], target: int, lo: int, hi: int) -> int:
    num = 0
    while lo < hi:
        mid = lo + (hi - lo) // 2
        cur_sum = nums[lo] + nums[hi]
        if cur_sum >= target:
            if nums[lo] + nums[mid] >= target:
                hi = mid - 1
            else:
                hi -= 1
        else:
            num += hi - lo
            lo += 1
    return num
        
def threeSumSmaller(self, nums: List[int], target: int) -> int:
    num = 0
    nums.sort()
    for i in range(len(nums) - 2):
        if 3 * nums[i] >= target:
            break
        num += self.twoSum(nums, target - nums[i], i + 1, len(nums) - 1)
    return num
```


### 18. 4Sum
Write a kSum(), where base case is twoSum(), in which we use two pointers.
Remember to **de-duplicate**!!
Time O(n^(k-1)) Space O(n) if Python's Timsort; O(1) if Java's quicksort.

```python
def twoSum(self, nums: List[int], target: int, start: int, solution: List[int], solutions: List[List[int]]):
    lo, hi = start, len(nums) - 1
    while lo < hi:
        cur_sum = nums[lo] + nums[hi]
        if cur_sum > target:
            hi -= 1
        elif cur_sum < target:
            lo += 1
        else:
            sol = copy.deepcopy(solution)
            sol.extend([nums[lo], nums[hi]])
            solutions.append(sol)
            lo += 1
            hi -= 1
            while lo < hi and nums[lo] == nums[lo - 1]:
                lo += 1
# Time O(n^(k-1)) Space O(n) for sorting
def kSum(self, nums: List[int], target: int, start: int, solution: List[int], solutions: List[List[int]], k: int):
    if start >= len(nums):
        return
    if k == 2:
        self.twoSum(nums, target, start, solution, solutions)
        return
    for i in range(start, len(nums) - k + 1):
        if k * nums[i] > target:
            break
        if i > start and nums[i] == nums[i - 1]:
            continue
        solution.append(nums[i])
        self.kSum(nums, target - nums[i], i + 1, solution, solutions, k - 1)
        solution.pop()
    
def fourSum(self, nums: List[int], target: int) -> List[List[int]]:
    nums.sort()
    solutions = []
    self.kSum(nums, target, 0, [], solutions, 4)
    return solutions
```
