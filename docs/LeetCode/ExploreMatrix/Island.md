------
This is really just a series of problems which fall into UndirectedGraph category; However, there are plenty of them, and they do have some special features, e.g. vertices are 4-way connected (or 8-way).

### 463. Island Perimeter
> The grid is completely surrounded by water, and there is exactly one island (i.e., one or more connected land cells).
> 
> The island doesn't have "lakes", meaning the water inside isn't connected to the water around the island. One cell is a square with side length 1. Determine the perimeter of the island.

DFS solution: Each time you step out on water or on the border, you get one unit of perimeter.

### 200. Number of Islands
> Given an m x n 2d grid map of '1's (land) and '0's (water), return the number of islands.
> 
> An island is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are all surrounded by water.

- DFS. Time O(mn) Space O(mn) in worst case where all are lands.
- BFS. Time O(mn) Space O(mn) in worst case where the island is of some fractal shape.
Space complexity is definitely not O(min(m, n)); It can certainly be larger than that, e.g.

<img src="./LeetCode/Images/200-1.png" alt="Image of 200. Number of Islands" height="300"/>

Here it is circumstance of a round shape, which goes linearly with m, n; If exploiting a somewhat fractal shape, it can go faster than O(m+n); At most it can reach O(mn), which I think is definitely possible.  
Actually an interesting problem might be to find the maximum possible BFS leaf size for a given NxN grid, where the BFS always starts in upper left corner.

### 695. Max Area of Island
> Find the maximum area of an island in the given 2D array. (If there is no island, the maximum area is 0.)
Just record the area when finding islands.

### 1254. Number of Closed Islands
> Given a 2D grid consists of 0s (land) and 1s (water).  An island is a maximal 4-directionally connected group of 0s and a closed island is an island totally (all left, top, right, bottom) surrounded by 1s.
> 
> Return the number of closed islands.

Check if an island reaches the border. (By checking if it ever steps out of order when dfs)  
Time O(RC), where R is num of rows, C is num of columns. Space O(RC)  
**Note:** A caveat is that if we return earlier, some cells of current island will be left unexplored; And will be counted as an additional island. (See comments in below code)

```java
class Solution {
  public static final int LAND = 0;
  public static final int WATER = 1;
  public static final int[][] DIRECTIONS = new int[][] {
      {-1, 0}, {1, 0}, {0, 1}, {0, -1}
    };
  private boolean[][] marked;
  public int closedIsland(int[][] grid) {
    marked = new boolean[grid.length][grid[0].length];
    int count = 0;
    for (int i = 0; i < grid.length; i++) {
      for (int j = 0; j < grid[i].length; j++) {
        if (marked[i][j] == false && grid[i][j] == LAND) {
          if (dfs(grid, i, j) == true) count++;
        }
      }
    }
    return count;
  }
  // return false if step out of bound
  private boolean dfs(int[][] grid, int row, int col) {
    if (row < 0 || row >= grid.length || col < 0 || col >= grid[row].length) {
      return false;
    }
    if (marked[row][col] || grid[row][col] == WATER) return true;
    marked[row][col] = true;
    boolean closed = true;
    for (int[] dir : DIRECTIONS) {
      if (dfs(grid, row + dir[0], col + dir[1]) == false) closed = false;
    }
    return closed;
    // Note!!! Below is INCORRECT!!! Because if we return early, then we might have not finished marking all cells of the current island, which will yield us redundant islands.
    // for (int[] dir : DIRECTIONS) {
    //   if (dfs(grid, row + dir[0], col + dir[1]) == false) return false;
    // }
    // return true;
  }
}
```

### 305. Number of Islands II
> A 2d grid map of m rows and n columns is initially filled with water. We may perform an addLand operation which turns the water at position (row, col) into a land. **Given a list of positions to operate, count the number of islands after each addLand operation**. An island is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are all surrounded by water.
> 
> Example:
> 
> Input: m = 3, n = 3, positions = [[0,0], [0,1], [1,2], [2,1]]
> Output: [1,1,2,3]

Use UnionFind. Time O(mn + k) Space O(mn)
O(mn) time is needed for initializing the UnionFind, O(klog*mn) is needed for find() and union() operations, which in pratice is O(k).

**Caution:** What's special here is that when initializing the UnionFind, something needs to be done to take land/water of a cell into account. One way is to initialize the ids to be -1 initially. And when a land is added, call set_land() method to make id[p] = p. See code below.

```python
class UnionFind:
    def __init__(self, n: int) -> None:
        self.id = [-1 for i in range(n)]
        self.sz = [1 for i in range(n)]
        self.count = 0
    def set_land(self, p: int) -> None:
        self.id[p] = p
        self.count += 1
    def is_land(self, p: int) -> bool:
        return self.id[p] >= 0
    def find(self, p: int) -> int:
        if p != self.id[p]:
            self.id[p] = self.find(self.id[p])
        return self.id[p]
    def union(self, p: int, q: int) -> None:
        i, j = self.find(p), self.find(q)
        if i == j:
            return
        if self.sz[i] < self.sz[j]:
            self.id[i] = j
            self.sz[j] += self.sz[i]
        else:
            self.id[j] = i
            self.sz[i] += self.sz[j]
        self.count -= 1
```

### 694. Number of Distinct Islands

> Count the number of distinct islands. An island is considered to be the same as another if and only if one island can be translated (and not rotated or reflected) to equal the other.

Use a set to store distinct shapes, where we represent shape by coordinates of lands relative to the top-left land, which is the first land that we visit for an island.
**Note:** List is mutable thus not hashable. Need to convert it to tuple.
```python
def numDistinctIslands(self, grid: List[List[int]]) -> int: 
    set_island = set()
    m, n = len(grid), len(grid[0])
    queue = deque()
    for i in range(m):
        for j in range(n):
            if grid[i][j] == self.LAND:
                cur_island = []
                queue.append((i, j))
                grid[i][j] = self.VISITED
                while len(queue) > 0:
                    r, c = queue.popleft()
                    for dr, dc in self.DIRECTIONS:
                        new_r, new_c = r + dr, c + dc
                        if self.inbound(m, n, new_r, new_c) and grid[new_r][new_c] == self.LAND:
                            queue.append((new_r, new_c))
                            cur_island.append((new_r - i, new_c - j))
                            grid[new_r][new_c] = self.VISITED     
                set_island.add(tuple(cur_island))
    return len(set_island)
```

### 711. Number of Distinct Islands II

> Count the number of distinct islands. An island is considered to be the same as another if they have the same shape, or have the same shape after rotation (90, 180, or 270 degrees only) or reflection (left/right direction or up/down direction).

Select one of 8 possible representations to store an island. (For each transoformation, first subtract all coordinates by the min_x and min_y; Then select the **maximum** ***sorted*** collection of cells)  
**Tips:** Use complex numbers in Python to conveniently apply rotation (by multiplying 1j), and reflection (by switching real and imag)  
Time O(mnlog(mn)) because of sorting, Space O(mn)

```python
class Solution(object):
    LAND = 1
    WATER = 0
    VISITED = 2
    DIRECTIONS = [(0, 1), (0, -1), (1, 0), (-1, 0)]
    def inbound(self, grid: List[List[int]], r: int, c: int) -> bool:
        return r >= 0 and r < len(grid) and c >= 0 and c < len(grid[r]) 
    
    def dfs(self, grid: List[List[int]], r: int, c: int, shape: List[complex]) -> None:
        if self.inbound(grid, r, c) and grid[r][c] == self.LAND:
            shape.add(complex(r, c))
            grid[r][c] = self.VISITED
            for dr, dc in self.DIRECTIONS:
                self.dfs(grid, r + dr, c + dc, shape)
                
    def translate(self, shape: List[complex]) -> str:
        w = complex(min([z.real for z in shape]), min([z.imag for z in shape]))
        return ''.join(sorted([str(z-w) for z in shape]))
    
    def canonical(self, shape: List[complex]) -> str:
        ans = ""
        for k in range(4):
            ans = max(ans, self.translate([z * (1j)**k for z in shape]))
            ans = max(ans, self.translate([complex(z.imag, z.real) * (1j)**k for z in shape]))
        return ans
    # Time O(mnlogmn) Space O(mn)
    def numDistinctIslands2(self, grid: List[List[int]]) -> int:
        set_shapes = set()
        for r in range(len(grid)):
            for c in range(len(grid[0])):
                if grid[r][c] == self.LAND:
                    shape = set()
                    self.dfs(grid, r, c, shape)
                    set_shapes.add(self.canonical(shape))

        return len(set_shapes)
```

### 827. Making A Large Island
> In a 2D grid of 0s and 1s, we change at most one 0 to a 1.
> 
> After, what is the size of the largest island? (An island is a 4-directionally connected group of 1s).

We can find all islands, have a list to store areas, and mark grid element to island id.  
We can traverse all 0s, see if it is surounded by two or more different islands, and update the maxArea.
**Note:** It is possible that changing one 0 can connect more than 2 (at most 4) islands.

### 1568. Minimum Number of Days to Disconnect Island
> The grid is said to be connected if we have exactly one island, otherwise is said disconnected.
> 
> In one day, we are allowed to change any single land cell (1) into a water cell (0).
> 
> Return the minimum number of days to disconnect the grid.

The key observation is that, for any island, there has to be some cells that have <= 2 neighbors.   
Return 0 if there exists no island, or multiple islands.  
Return 1 if there is exactly one island and it has an **articulation point**. Use **Tarjan's algorithm** to find articulation point while DFS.  
Return 2 otherwise. (a special case is when there is one island with two lands, we need to remove both)  

Find articulation points / cut vertices:
Have an additional boolean array to record if a vertex is an articulation point or not. Have an integer to record number of edges going out from the start point during DFS. Need to pass root into the method.   
if (root == pre) numEdgeOut++;  
1. For vertices other than the start point, it is an articulation point if **id[cur] <= lo[new]**.
2. For **the start point**, it is an articulation point if **numEdgeOut > 1**. (not numNeighbors)

```java
class Solution {
  public static final int[][] DIRECTIONS = new int[][] {
    {-1, 0}, {1, 0}, {0, -1}, {0, 1}
  };
  public static final int WATER = 0;
  private int numLink;
  private int time;
  private int numLand;
  private boolean hasArticulationPoint;
  // Time O(mn) Space O(mn)
  public int minDays(int[][] grid) {
    int m = grid.length, n = grid[0].length;
    hasArticulationPoint = false;
    numLand = 0;
    int[][] id = new int[m][n];
    int[][] lo = new int[m][n];
    for (int i = 0; i < m; i++) {
      for (int j = 0; j < n; j++) {
        if (grid[i][j] == 1 && id[i][j] == 0) {
          if (numLand == 0) {
            numLink = 0;
            time = 0;
            dfs(grid, i, j, i, j, -1, -1, id, lo);
            if (numLink > 1) hasArticulationPoint = true;
          }
          else { // Another land found aside from an island.
            return 0;
          }
        }
      }
    }
    if (numLand < 2) return numLand; // No island; Or only one land.
    if (hasArticulationPoint) return 1;
    return 2;
  }
  private void dfs(int[][] grid, int rootI, int rootJ, int curI, int curJ, int preI, int preJ, int[][] id, int[][] lo) {
    if (preI == rootI && preJ == rootJ) numLink++;
    id[curI][curJ] = ++time;
    lo[curI][curJ] = id[curI][curJ];
    numLand++;
    for (int[] dir : DIRECTIONS) {
      int nextI = curI + dir[0];
      int nextJ = curJ + dir[1];
      if (!inBound(grid, nextI, nextJ) || grid[nextI][nextJ] == WATER) continue;
      if (nextI == preI && nextJ == preJ) continue;
      if (id[nextI][nextJ] == 0) {
        dfs(grid, rootI, rootJ, nextI, nextJ, curI, curJ, id, lo);
        lo[curI][curJ] = Math.min(lo[curI][curJ], lo[nextI][nextJ]);
        if ((curI != rootI || curJ != rootJ) && id[curI][curJ] <= lo[nextI][nextJ]) {
          hasArticulationPoint = true;
        }
      } else {
        lo[curI][curJ] = Math.min(lo[curI][curJ], id[nextI][nextJ]);
      }
    }
  }
  private boolean inBound(int[][] grid, int i, int j) {
    return i >= 0 && i < grid.length && j >= 0 && j < grid[i].length;
  }
}
```
