------
### Screenshots from a Youtube video
[Bridges and Articulation Points (20min)](https://www.youtube.com/watch?v=aZXi1unBdJA)

<img src="./LeetCode/Images/1192-1.png" alt="Tarjan's Algorithm" height="300"/>
<img src="./LeetCode/Images/1192-2.png" alt="Tarjan's Algorithm" height="300"/>
<img src="./LeetCode/Images/1192-3.png" alt="Tarjan's Algorithm" height="200"/>
<img src="./LeetCode/Images/1192-4.png" alt="Tarjan's Algorithm" height="300"/>
<img src="./LeetCode/Images/1192-5.png" alt="Tarjan's Algorithm" height="400"/>
<img src="./LeetCode/Images/1192-6.png" alt="Tarjan's Algorithm" height="400"/>
<img src="./LeetCode/Images/1192-7.png" alt="Tarjan's Algorithm" height="400"/>
<img src="./LeetCode/Images/1192-8.png" alt="Tarjan's Algorithm" height="400"/>
<img src="./LeetCode/Images/1192-9.png" alt="Tarjan's Algorithm" height="400"/>

### Find bridges/cut edges:
Have two arrays id, and lo. id records time when a vertex is first visited; lo records smallest id a vertex can reach **following paths of DFS**.
Things to do in dfs() method:
1. Initialize id and lo for current vertex.
2. Visit all neighbors, avoiding its parent vertex (where it came from); Updating lo for current vertex depending on if a neighbor was visited before or not.
3. For a newly visited neighbor, if **id[cur] < lo[new]**, then cur--new is a bridge.

Intuition: a parent node tells its child: Go get the eldest node that you can find.  
If the child fetches someone at least as old as the parent, well the parent--child edge is not a must.  
If the child can not **(id[cur] < lo[new])**, then the parent cannot abandon the child.

### Find articulation points / cut vertices:
Have an additional boolean array to record if a vertex is an articulation point or not. Have an integer to record number of edges going out from the start point during DFS. Need to pass root into the method.   
if (root == pre) numEdgeOut++;  
1. For vertices other than the start point, it is an articulation point if **id[cur] <= lo[new]**.
2. For **the start point**, it is an articulation point if **numEdgeOut > 1**. (not numNeighbors)

### 1192. Critical Connections in a Network
> There are n servers numbered from 0 to n-1 connected by undirected server-to-server connections forming a network where connections[i] = [a, b] represents a connection between servers a and b. Any server can reach any other server directly or indirectly through the network.
> 
> A critical connection is a connection that, if removed, will make some server unable to reach some other server.
> 
> Return all critical connections in the network in any order.

Use Tarjan's algorithm, Time O(V+E) Space O(V+E)

```java
class Solution {
  // id := visit order
  // lo := smallest id a node can reach following links in our dfs, including itself
  private int time;
  public List<List<Integer>> criticalConnections(int n, List<List<Integer>> connections) {
    int[] id = new int[n];
    int[] lo = new int[n];
    Arrays.fill(id, -1);
    List<Integer>[] adj = (ArrayList<Integer>[]) new ArrayList[n];
    List<List<Integer>> res = new ArrayList<>();
    for (int i = 0; i < n; i++) {
      adj[i] = new ArrayList<>();
    }
    // construct adjacacy list
    for (List<Integer> connection : connections) {
      int one = connection.get(0), other = connection.get(1);
      adj[one].add(other);
      adj[other].add(one);
    }
    time = 0;
    for (int i = 0; i < n; i++) {
      if (id[i] == -1) {
        dfs(adj, -1, i, id, lo, res);
      }
    }
    return res;
  }
  private void dfs(List<Integer>[] adj, int parent, int cur, int[] id, int[] lo, List<List<Integer>> res) {
    id[cur] = time++;
    lo[cur] = id[cur];
    for (int nei : adj[cur]) {
      if (nei == parent) continue;
      if (id[nei] == -1) {
        dfs(adj, cur, nei, id, lo, res);
        lo[cur] = Math.min(lo[cur], lo[nei]);
        if (id[cur] < lo[nei]) {
          res.add(Arrays.asList(cur, nei));
        }
      } else {
        lo[cur] = Math.min(lo[cur], id[nei]);
      }
    }
  }
}
```

### 1568. Minimum Number of Days to Disconnect Island
> Given a 2D grid consisting of 1s (land) and 0s (water).  An island is a maximal 4-directionally (horizontal or vertical) connected group of 1s.
> 
> The grid is said to be connected if we have exactly one island, otherwise is said disconnected.
> 
> In one day, we are allowed to change any single land cell (1) into a water cell (0).
> 
> Return the minimum number of days to disconnect the grid.

```python
class Solution:
    # Use Tarjan's Algorithm to find articulation point. Time O(mn) Space O(mn)
    DIRECTIONS = [(0, 1), (0, -1), (1, 0), (-1, 0)]
    LAND, WATER, UNVISITED = 1, 0, -1
    def __init__(self):
        self.id, self.lo = None, None
        self.num_land, self.time, self.num_edge_out = 0, 0, 0
        self.hasArticulationPoint = False
    def dfs(self, grid: List[List[int]], root_i: int, root_j: int, cur_i: int, cur_j: int, pre_i: int, pre_j: int) -> None:
        if (pre_i, pre_j) == (root_i, root_j):
            self.num_edge_out += 1
        self.id[cur_i][cur_j] = self.lo[cur_i][cur_j] = self.time
        self.time += 1
        self.num_land += 1
        for (di, dj) in self.DIRECTIONS:
            next_i, next_j = cur_i + di, cur_j + dj
            if self.out_of_bound(grid, next_i, next_j) or grid[next_i][next_j] == self.WATER or (next_i, next_j) == (pre_i, pre_j):
                continue
            if self.id[next_i][next_j] == self.UNVISITED:
                self.dfs(grid, root_i, root_j, next_i, next_j, cur_i, cur_j)
                self.lo[cur_i][cur_j] = min(self.lo[cur_i][cur_j], self.lo[next_i][next_j])
                if (cur_i, cur_j) != (root_i, root_j) and self.id[cur_i][cur_j] <= self.lo[next_i][next_j]:
                    self.hasArticulationPoint = True
            else:
                self.lo[cur_i][cur_j] = min(self.lo[cur_i][cur_j], self.id[next_i][next_j])
    def minDays(self, grid: List[List[int]]) -> int:
        m, n = len(grid), len(grid[0])
        self.id = [[self.UNVISITED for i in row] for row in grid]
        self.lo = [[self.UNVISITED for i in row] for row in grid]
        for i in range(m):
            for j in range(n):
                if grid[i][j] == self.LAND and self.id[i][j] == self.UNVISITED:
                    self.time, self.num_edge_out = 0, 0
                    if self.num_land == 0:
                        self.dfs(grid, i, j, i, j, -1, -1)
                        self.hasArticulationPoint |= self.num_edge_out > 1
                    else:
                        return 0
        if self.num_land < 2:
            return self.num_land
        if self.hasArticulationPoint:
            return 1
        return 2
    
    def out_of_bound(self, grid: List[List[int]], i: int, j: int) -> bool:
        return i < 0 or i >= len(grid) or j < 0 or j >= len(grid[i])
```
