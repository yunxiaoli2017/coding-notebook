------
## Basics
### Iterative traversal

```python
class Solution:
    def inorderTraversal(self, root: TreeNode) -> List[int]:
        if not root:
            return []
        stack, res = deque(), []
        while stack or root:
            while root:
                stack.append(root)
                root = root.left
            node = stack.pop()
            res.append(node.val)
            root = node.right
        return res
```

```python
class Solution:
    def postorderTraversal(self, root: TreeNode) -> List[int]:
        if not root:
            return []
        stack, res = deque(), []
        stack.append(root)
        pre = None
        while stack:
            cur = stack[-1]
            if pre is None or pre.left == cur or pre.right == cur:
                if cur.left:
                    stack.append(cur.left)
                elif cur.right:
                    stack.append(cur.right)
                else:
                    stack.pop()
                    res.append(cur.val)
            elif pre == cur.left:
                if cur.right:
                    stack.append(cur.right)
                else:
                    stack.pop()
                    res.append(cur.val)
            else:
                stack.pop()
                res.append(cur.val)
            pre = cur
        return res
```

### Morris traversal
```java
class Solution {
  public List<Integer> inorderTraversal(TreeNode root) {
    List<Integer> res = new ArrayList<>();
    TreeNode cur = root;
    while (cur != null) {
      // if does not have left child, go to right (successor)
      if (cur.left == null) {
        res.add(cur.val);
        cur = cur.right;
      } else {
        // find precursor of cur, it should be the rightmost node in its left-subtree
        // could be itself, which means we have traversed the left-subtree
        TreeNode pre = cur.left;
        while (pre.right != null && pre.right != cur) {
          pre = pre.right;
        }
        if (pre.right == null) {
          // if preorder, output before diving into left subtree
          // res.add(cur.val)
          pre.right = cur;
          cur = cur.left;
        } else {
          res.add(cur.val);
          pre.right = null;
          cur = cur.right;
        }
      }
    }
    return res;
  }
}
```
##### 144. Binary Tree Preorder Traversal <!-- {docsify-ignore} -->
##### 94. Binary Tree Inorder Traversal <!-- {docsify-ignore} -->
##### 145. Binary Tree Postorder Traversal <!-- {docsify-ignore} -->
##### 102. Binary Tree Level Order Traversal <!-- {docsify-ignore} -->
##### 107. Binary Tree Level Order Traversal II (from leaf to root) <!-- {docsify-ignore} -->
##### 589. N-ary Tree Preorder Traversal <!-- {docsify-ignore} -->
##### 590. N-ary Tree Postorder Traversal <!-- {docsify-ignore} -->
##### 429. N-ary Tree Level Order Traversal <!-- {docsify-ignore} -->

## Special
### Zigzag level order 
### 103. Binary Tree Zigzag Level Order Traversal <!-- {docsify-ignore} -->
> Given a binary tree, return the zigzag level order traversal of its nodes' values. (ie, from left to right, then right to left for the next level and alternate between).

```python
class Solution:
    # 取这层时放下层；下层放到另一边；下层顺序这时定：想从左开始，就先放右孩子；想从右开始，就先放左孩子。
    # pop cur level from left, then append next level to the right. 
    # If you want the next level to be from left to right, then you should append right child first.
    def zigzagLevelOrder(self, root: TreeNode) -> List[List[int]]:
        if not root:
            return []
        deque = collections.deque()
        deque.append(root)
        res = []
        left_to_right = False
        while deque:
            cur_level = []
            num_cur_node = len(deque)
            while num_cur_node > 0:
                if left_to_right:
                    node = deque.popleft()
                    if node.right:
                        deque.append(node.right)
                    if node.left:
                        deque.append(node.left)
                else:
                    node = deque.pop()
                    if node.left:
                        deque.appendleft(node.left)
                    if node.right:
                        deque.appendleft(node.right)
                num_cur_node -= 1
                cur_level.append(node.val)
            left_to_right = not left_to_right
            res.append(cur_level)
        return res
```
### Vertical order
### 314. Binary Tree Vertical Order Traversal <!-- {docsify-ignore} -->
> Given a binary tree, return the vertical order traversal of its nodes' values. (ie, from top to bottom, column by column).
> 
> If two nodes are in the same row and column, the order should be **from left to right**.
### 987. Vertical Order Traversal of a Binary Tree <!-- {docsify-ignore} -->
> Given a binary tree, return the vertical order traversal of its nodes values.
  
> If two nodes have the same position, the order should be **from smallest to largest**.

```python
class Solution:
    # BFS, keep track of column; store values in a map
    # update min_col and max_col along the way
    def verticalOrder(self, root: TreeNode) -> List[List[int]]:
        if not root:
            return []
        queue = collections.deque()
        dict_col = {}
        res = []
        min_col, max_col = 0, 0
        queue.append((root, 0))
        # row = 0   # For 987
        while queue:
            size = len(queue)
            while size > 0:
                size -= 1
                (node, col) = queue.popleft()
                min_col = min(min_col, col)
                max_col = max(max_col, col)
                if node.left:
                    queue.append((node.left, col - 1))
                if node.right:
                    queue.append((node.right, col + 1))
                if col in dict_col:
                    # dict_col[col].append((node.val, row))   # For 987.
                    dict_col[col].append(node.val)
                else:
                    # dict_col[col] = [(node.val, row)]   # For 987.
                    dict_col[col] = [node.val]
            # row += 1   # For 987.
                
        for i in range(min_col, max_col + 1):
            # For 987. need to sort nodes in a colomn, first by row and then by value
            # dict_col[i].sort(key = lambda x: (x[1], x[0]))
            res.append(dict_col[i])
        return res
```
### Right side view
### 199. Binary Tree Right Side View <!-- {docsify-ignore} -->
> Given a binary tree, imagine yourself standing on the right side of it, return the values of the nodes you can see ordered from top to bottom.

Traverse in the order self-right-left.   
Record depth while traversing.   
Add value into a list when depth >= list.size()   
### Leaf order (height order)
### 366. Find Leaves of Binary Tree <!-- {docsify-ignore} -->
> Given a binary tree, collect a tree's nodes as if you were doing this: Collect and remove all leaves, repeat until the tree is empty.

This is actually order by node height counting from the leaves. (Level-order is by node depth counting from the root)

```python
class Solution:
    
    def dfs(self, root: TreeNode, res: List[List[int]]) -> int:
        if not root:
            return -1
        left_height = self.dfs(root.left, res)
        right_height = self.dfs(root.right, res)
        cur_height = max(left_height, right_height) + 1
        while len(res) <= cur_height:
            res.append([])
        res[cur_height].append(root.val)
        return cur_height
    # Time O(n) Space O(height)
    def findLeaves(self, root: TreeNode) -> List[List[int]]:
        if not root:
            return []
        res = []
        self.dfs(root, res)
        return res
```

## Traverse for Info

### 98. Validate Binary Search Tree
> Given the root of a binary tree, determine if it is a valid binary search tree (BST).

- You can ask left and right subtrees: what are your min and max val there? (Will need to return several things)
- Or, you can pass (lo, hi) boundary down to your childs, and let them return whether they are valid.

Note: when you need to do +1 or -1 on int, you probably want to have long.

### 99. Recover Binary Search Tree (hard)
> You are given the root of a binary search tree (BST), where exactly two nodes of the tree were swapped by mistake. Recover the tree without changing its structure.
> 
> Follow up: A solution using O(n) space is pretty straight forward. Could you devise a constant space solution?

For O(1) space, we have to resort to Morris traversal.

```python
class Solution:
    
    def swap(self, one: TreeNode, other: TreeNode) -> None:
        one.val, other.val = other.val, one.val
        
    def recoverTree(self, root: TreeNode) -> None:
        """does Morris inorder traversal while detecting inverted pair."""
        cur, pre = root, None
        swapped_smaller, swapped_larger = None, None
        while cur:
            if cur.left:
                precursor = cur.left
                while precursor.right is not None and precursor.right != cur:
                    precursor = precursor.right
                if precursor.right is None:
                    precursor.right = cur
                    cur = cur.left
                else:
                    precursor.right = None
                    # keep the larger one that we first encounter
                    # but update the smaller one if we observe another dip
                    if pre and pre.val > cur.val:
                        swapped_smaller = cur
                        if not swapped_larger:
                            swapped_larger = pre
                    pre = cur
                    cur = cur.right
            else:
                if pre and pre.val > cur.val:
                        swapped_smaller = cur
                        if not swapped_larger:
                            swapped_larger = pre
                pre = cur
                cur = cur.right
        self.swap(swapped_larger, swapped_smaller)
```
