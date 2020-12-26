------

### 210. Course Schedule II
1. Construct digraph.
```java
List<Integer>[] adj = (ArrayList<Integer>[]) new ArrayList[V];
```
2. Dfs traversal. Find loop while recording reverse post order. Maintain `boolean[] onStack[V]` and `List<Integer> order`

### 332. Reconstruct Itinerary
Topological order. Here we are required to use all tickets, that is to traverse all the edges. In the book, we have a `boolean[V]` marked to record if a vertice has been visited. Here we could have a `List<String> toList` to store all destinations from a start city, and remove the one we traverse, which effectively destroys that ticket.
Note that in all possible orders, we are required to return the one which mostly resembles lexicographic order (alphabetic order). We can achieve this by choosing the alphabetically smallest destination as the next city traversed.

### 269. Alien Dictionary
There is a new alien language which uses the latin alphabet. However, the order among letters are unknown to you. You receive a list of non-empty words from the dictionary, where words are sorted lexicographically by the rules of this new language. Derive the order of letters in this language.
Ask about what to return.
Remember to check for loop, which represents invalid input.
 - Do I need to include letters that do not appear in words?
 - Do I need to include letters that appeared but do not have a certain relation between any other letter? (Perhaps the only case: words = ["zz", "zzz"])
