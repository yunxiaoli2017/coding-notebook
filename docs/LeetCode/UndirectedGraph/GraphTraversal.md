------

### 127. Word Ladder
Given two words (`beginWord` and `endWord`), and a `wordList`, find the length of shortest transformation sequence from `beginWord` to `endWord`, such that:
- Only one letter can be changed for each transformation.
- Each transformed word must exist in the `wordList`.

Insights:
- `beginWord` and `endWord` are symmetric. We should optimize it with two-end BFS, where we have a beginSet and an endSet, and choose one to expand based on size.
- The correct way to traverse:
  - For each word, try all possible transformations. `O(26L)`
  - For each word, traverse all existing words and check if reachable or not. `O(NL)`
  - If we preprocess the `wordList` and store it as adjacent list, which take `Time O(N^2 L) Space O(N^2)`. Then for each word, we can get all its neighbors in `O(1)`. This will be optimal if we were to perform this task multiple times.

### 126. Word Ladder II
In order to return all valid paths, dfs is a must. It then becomes critical to prune unnecessary branches for better time performance.

#### Two choices: 
  
Either  
1. Use BFS to find the shortest distance between `beginWord` and `endWord`, meanwhile store distances of visited nodes to `beginWord`, (optinal) and store node's neighbors in a HashMap for later use;
2. Use DFS to output paths with the same distance as the shortest distance from distance HashMap: compare if the distance of the next level node equals the distance of the current node + 1.  

Or
  
1. Use BFS to find the shortest distance between `beginWord` and `endWord`, meanwhile construct a **graph** as adjacency list, that have **all the connections traversed while BFS**.
> **CRITICAL**: nodes at the same level should be allowed to be visited multiple times; Be careful when checking if a node has been visited; One solution is to use a `Set<String> curLevel` to store all nodes at current level and after finishing current level, add all these nodes to `Set<String> visited`, also add these nodes to `Queue<String> queue`  
2. Use DFS to traverse the constructed graph, by doing so we avoid visiting any unnecessary connections.  

#### One lesson:  
**Not** particularly useful to map word to index for space usage. Afterall, we are only parsing String's references around.

### 785. Is Graph Bipartite?
The graph can consist of several independent sub-graphs. Start from all vertices to ensure traversing the whole graph.
