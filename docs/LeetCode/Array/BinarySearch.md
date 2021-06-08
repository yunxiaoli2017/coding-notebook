------

### 4. Median of Two Sorted Arrays

Suppose we are to find kth smallest element.  
Equivalently we are to find the first k smallest elements. There are two arrays to pick elements from, and since they are sorted, we pick from the left side. We are to pick k elements in total, that means, we can pick at least k / 2 elements from one of the two arrays; (Otherwise we won't be able to get k elements.)  
Then the question becomes: which array to pick k / 2 from? The smaller one.  
We have reduced the problem of kth smallest to (k - k / 2)th smallest; By applying this recursively, we can solve the problem.  
Time O(log(m + n)) Space O(log(m + n)) If stack space is still concerned, could rewrite in iterative way, then space would be O(1)  

```java
public double findMedianSortedArrays(int[] nums1, int[] nums2) {
  int total = nums1.length + nums2.length;
  if (total % 2 == 0) {
    return (double) (findKthSmallest(nums1, 0, nums2, 0, total / 2) + findKthSmallest(nums1, 0, nums2, 0, total / 2 + 1)) / 2;
  } else {
    return findKthSmallest(nums1, 0, nums2, 0, total / 2 + 1);
  }
}
```

### 33. Search in Rotated Sorted Array

> You are given an integer array nums sorted in ascending order, and an integer target.
> 
> Suppose that nums is rotated at some pivot unknown to you beforehand (i.e., [0,1,2,4,5,6,7] might become [4,5,6,7,0,1,2]).
> 
> If target is found in the array return its index, otherwise, return -1.
> 
> Constraints:
> - All values of nums are **unique**.

- Binary search for minimum number.
- Binary search for the target in one of two segments.

### 81. Search in Rotated Sorted Array II

> Follow-up on `33`, what if there are duplicates?

- Binary search for minimum number. Then locate the first occurence of minimum number. 
- Binary search for the target in one of two segments.

```java
  public boolean search(int[] nums, int target) {
    ...
    int rotate = binarySearchRotateIndex(nums, 0, nums.length - 1);
    while (rotate >= 1 && nums[rotate] == nums[rotate - 1]) rotate--;
    ...
  }
    
  private int binarySearchRotateIndex(int[] nums, int lo, int hi) {
    if (nums[lo] < nums[hi]) return 0;
    while (lo <= hi) {
      int mid = lo + (hi - lo) / 2;
      if (mid == nums.length - 1) return mid; // added this line to avoid ArrayIndexOutOfBoundsException
      if (nums[mid] > nums[mid + 1]) {
        return mid + 1;
      } else if (nums[mid] >= nums[lo]) {
        lo = mid + 1;
      } else {
        hi = mid - 1;
      }
    }
    return 0;
  }
```

### 34. Find First and Last Position of Element in Sorted Array

### 74. Search a 2D Matrix
Sorted; Just treat it as an array and do binary search.
