------
## Basics
### Iterative traversal


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
### 144. Binary Tree Preorder Traversal <!-- {docsify-ignore} -->
### 94. Binary Tree Inorder Traversal <!-- {docsify-ignore} -->
### 145. Binary Tree Postorder Traversal <!-- {docsify-ignore} -->
### 102. Binary Tree Level Order Traversal <!-- {docsify-ignore} -->
### 107. Binary Tree Level Order Traversal II (from leaf to root) <!-- {docsify-ignore} -->
### 589. N-ary Tree Preorder Traversal <!-- {docsify-ignore} -->
### 590. N-ary Tree Postorder Traversal <!-- {docsify-ignore} -->
### 429. N-ary Tree Level Order Traversal <!-- {docsify-ignore} -->

## Special
### 103. Binary Tree Zigzag Level Order Traversal


### 199. Binary Tree Right Side View
> Given a binary tree, imagine yourself standing on the right side of it, return the values of the nodes you can see ordered from top to bottom.

Traverse in the order self-right-left.   
Record depth while traversing.   
Add value into a list when depth >= list.size()   

 
