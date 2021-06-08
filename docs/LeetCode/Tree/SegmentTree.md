------

### 307. Range Sum Query - Mutable
> Given an integer array nums, find the sum of the elements between indices i and j (i ≤ j), inclusive.
> 
> The update(i, val) function modifies nums by updating the element at index i to val.

SegmentTree.  build O(n); update a single element O(logn); query O(logn)   
If need to update all elements of a range by the same change, then could use lazy update to save time. (Use an array lazy[] to hold change to all elements of a segment, but do not go deeper to apply it; Only apply it when visiting it directly, that is, when needed; And reset lazy[i] once updated node i)  

```java
class NumArray {
  // segment tree
  private int[] seg;
  private int n;
  public NumArray(int[] nums) {
    this.n = nums.length;
    if (n > 0) {
      this.seg = new int[2 * n];
      buildSegTree(nums);
    }
  }
  private void buildSegTree(int[] nums) {
    for (int i = 0; i < n; i++) {
      seg[n + i] = nums[i];
    }
    for (int i = n - 1; i > 0; i--) {
      seg[i] = seg[2 * i] + seg[2 * i + 1];
    }
  }
  public void update(int i, int val) {
    if (i >= n) return;
    int index = i + n;
    int diff = val - seg[index];
    while (index > 0) {
      seg[index] += diff;
      index /= 2;
    }
  }
  public int sumRange(int i, int j) {
    if (j >= n) return 0;
    int lo = i + n, hi = j + n; // start from leaf nodes
    int sum = 0;
    while (lo <= hi) {
      if (lo % 2 == 1) { // right child, do not want this parent
        sum += seg[lo];
        lo++; // move right to the adjacent node on the same level
      }
      if (hi % 2 == 0) { // left child, do not want this parent
        sum += seg[hi];
        hi--; // move left to the adjacent node on the same level
      }
      // Here we want parents of lo and hi
      lo /= 2;
      hi /= 2;
    }
    return sum;
  }
}
```
