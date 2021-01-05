------

### 427. Construct Quad Tree
> Given a n * n matrix grid of 0's and 1's only. We want to represent the grid with a Quad-Tree.
> 
> Return the root of the Quad-Tree representing the grid.
> 
> Notice that you can assign the value of a node to True or False when isLeaf is False, and both are accepted in the answer.
> 
> A Quad-Tree is a tree data structure in which each internal node has exactly four children. Besides, each node has two attributes:
> 
> val: True if the node represents a grid of 1's or False if the node represents a grid of 0's. 
> isLeaf: True if the node is leaf node on the tree or False if the node has the four children.
> ```java
> class Node {
>     public boolean val;
>     public boolean isLeaf;
>     public Node topLeft;
>     public Node topRight;
>     public Node bottomLeft;
>     public Node bottomRight;
> }
> ```
> We can construct a Quad-Tree from a two-dimensional area using the following steps:
> 
> If the current grid has the same value (i.e all 1's or all 0's) set isLeaf True and set val to the value of > the grid and set the four children to Null and stop.
> If the current grid has different values, set isLeaf to False and set val to any value and divide the current > grid into four sub-grids as shown in the photo.
> Recurse for each of the children with the proper sub-grid.
> If you want to know more about the Quad-Tree, you can refer to the wiki.

```python
class Solution:
    
    def helper(self, grid: List[List[int]], row: int, col: int, size: int) -> 'Node':
        """
        construct a quad tree with a size-by-size block starting at (row, col)
        """
        if size == 0:
            return None
        if size == 1:
            return Node(grid[row][col], True)
        size //= 2
        topLeft = self.helper(grid, row, col, size)
        topRight = self.helper(grid, row, col + size, size)
        bottomLeft = self.helper(grid, row + size, col, size)
        bottomRight = self.helper(grid, row + size, col + size, size)
        if all([topLeft.isLeaf, topRight.isLeaf, 
                bottomLeft.isLeaf, bottomRight.isLeaf, 
                topLeft.val == topRight.val == bottomLeft.val == bottomRight.val]):
            return Node(topLeft.val, True)
        return Node(0, False, topLeft, topRight, bottomLeft, bottomRight)
    # Time O(n) Space O(logn)
    def construct(self, grid: List[List[int]]) -> 'Node':
        if len(grid) == 0 or len(grid[0]) == 0:
            return None
        return self.helper(grid, 0, 0, len(grid))
```
