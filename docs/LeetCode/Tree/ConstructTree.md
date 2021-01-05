------
### 1008. Construct Binary Search Tree from Preorder Traversal
### 105. Construct Binary Tree from Preorder and Inorder Traversal
> Given preorder and inorder traversal of a tree, construct the binary tree.  
> Note:
> You may assume that duplicates do not exist in the tree.

```python
class Solution:
    
    def __init__(self):
        self.preorder = []
        self.inorder = []
        self._preorder_index = 0
        self._inorder_index = 0
        
    def helper(self, stop: int) -> TreeNode:
        if self._inorder_index >= len(self.inorder):
            return None
        if self.inorder[self._inorder_index] == stop:
            # When _inorder_index meets the stop value; 
            # That means there is no node to be constructed at this place (reached the bottom already)
            self._inorder_index += 1
            return None
        node = TreeNode(self.preorder[self._preorder_index])
        self._preorder_index += 1
        node.left = self.helper(node.val)
        node.right = self.helper(stop)
        return node
        
    def bstFromPreorder(self, preorder: List[int]) -> TreeNode:
        if not preorder:
            return None
        self.preorder = preorder
        self.inorder = sorted(preorder)
        return self.helper(sys.maxsize)
```

### 106. Construct Binary Tree from Inorder and Postorder Traversal
> Given inorder and postorder traversal of a tree, construct the binary tree.  
> Note:
> You may assume that duplicates do not exist in the tree.
```python
class Solution:
    
    def __init__(self):
        self._postorder_index = 0
        self.inorder = None
        self.postorder = None
        self._inorder_index_by_value = None
        
    def dfs(self, lo: int, hi: int) -> TreeNode:
        if lo > hi or self._postorder_index < 0:
            return
        node = TreeNode(self.postorder[self._postorder_index])
        self._postorder_index -= 1
        mid = self._inorder_index_by_value[node.val]
        node.right = self.dfs(mid + 1, hi)
        node.left = self.dfs(lo, mid - 1)
        return node
        
    def buildTree(self, inorder: List[int], postorder: List[int]) -> TreeNode:
        if len(inorder) == 0:
            return None
        self._postorder_index = len(postorder) - 1
        self.inorder, self.postorder = inorder, postorder
        self._inorder_index_by_value = {}
        for index, val in enumerate(inorder):
            self._inorder_index_by_value[val] = index
        return self.dfs(0, len(inorder) - 1)
```

### 889. Construct Binary Tree from Preorder and Postorder Traversal
> Return any binary tree that matches the given preorder and postorder traversals.
> 
> Values in the traversals pre and post are distinct positive integers.

Size of left subtree can be determined by matching leading head in preorder and trailing head in postorder.

```python
class Solution:
    # construct a tree with (0, len(pre))
    # solve this recursively, which part of preorder and postorder correspond to left subtree? The left subtree region can be specified by three parameters: start_index in preorder, start_index in postorder, and size of the left subtree
    # pre[1] and post[0] are start index of left subtree
    # if post[l] == pre[1], then size of left subtree is l + 1
    def __init__(self):
        self.pre = []
        self.post = []
        self._post_index_by_val = {}
    
    def constructTree(self, pre_lo, post_lo, size):
        """
        construct a tree given its preorder as self.pre[pre_lo : pre_lo+size-1], and postorder as self.post[post_lo : post_lo+size-1]
        """
        if size == 0:
            return None
        node = TreeNode(self.pre[pre_lo])
        if size == 1: # neccesary, otherwise will indexOutOfRange
            return node
        left_subtree_size = self._post_index_by_val[self.pre[pre_lo + 1]] - post_lo + 1
        node.left = self.constructTree(pre_lo + 1, post_lo, left_subtree_size)
        node.right = self.constructTree(pre_lo + 1 + left_subtree_size, post_lo + left_subtree_size, size - 1 - left_subtree_size)
        return node
    # Time O(n) Space O(n)
    def constructFromPrePost(self, pre: List[int], post: List[int]) -> TreeNode:
        if len(pre) == 0:
            return None
        self.pre, self.post = pre, post
        for index, val in enumerate(post):
            self._post_index_by_val[val] = index
        return self.constructTree(0, 0, len(pre))
```

### 606. Construct String from Binary Tree
> You need to construct a string consists of parenthesis and integers from a binary tree with the preorder > traversing way.
> 
> The null node needs to be represented by empty parenthesis pair "()". And **you need to omit all the empty parenthesis pairs that don't affect the one-to-one mapping relationship between the string and the original binary tree.**

Tips:
1. When is an empty () neccesary even if no values in that subtree? When there is right subtree but left subtree is empty.
2. Avoid using += while recursing. Since strings are immutable, this creates unnecessary temporary objects and results in quadratic rather than linear running time. Instead, add each substring to a list and ''.join the list after the loop terminates (or, write each substring to an io.BytesIO buffer).

```python
class Solution:
    
    def dfs(self, node: TreeNode, res: List[str]) -> None:
        if not node:
            return
        res.append(str(node.val))
        if node.left or node.right:
            res.append("(")
            self.dfs(node.left, res)
            res.append(")")
        if node.right:
            res.append("(")
            self.dfs(node.right, res)
            res.append(")")
        
    def tree2str(self, t: TreeNode) -> str:
        if not t:
            return ""
        res = []
        self.dfs(t, res)
        return "".join(res)
```
