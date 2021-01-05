------

### 79. Word Search
> Given an m x n board and a word, find if the word exists in the grid.
> 
> The word can be constructed from letters of sequentially adjacent cells, where "adjacent" cells are horizontally or vertically neighboring. The same letter cell may not be used more than once.

Time O(N * 4 * 3^(L-1)), N is number of cells in the board, L is word length
Space O(L)

```python
class Solution:
    DIRECTIONS = [(0, 1), (0, -1), (1, 0), (-1, 0)]
    VISITED = "#"
    def __init__(self):
        self.board = None
        self.word = None
        
    def _dfs(self, row: int, col: int, letter_index: int) -> bool:
        if (self._out_of_bound(row, col) or 
            self.board[row][col] != self.word[letter_index]):
            return False
        if letter_index == len(self.word) - 1:
            return True
        original_letter = self.board[row][col]
        self.board[row][col] = self.VISITED
        for dr, dc in self.DIRECTIONS:
            if self._dfs(row + dr, col + dc, letter_index + 1):
                self.board[row][col] = original_letter
                return True
        self.board[row][col] = original_letter
        return False
    
    def _out_of_bound(self, row: int, col: int) -> bool:
        return (row < 0 or row >= len(self.board) or 
                col < 0 or col >= len(self.board[row]))
    
    def exist(self, board: List[List[str]], word: str) -> bool:
        m, n = len(board), len(board[0])
        self.board, self.word = board, word
        for row in range(m):
            for col in range(n):
                if self._dfs(row, col, 0):
                    return True
        return False
```

### 212. Word Search II
> Given an m x n board of characters and a list of strings words, return all words on the board.
> 
> Each word must be constructed from letters of sequentially adjacent cells, where adjacent cells are horizontally or vertically neighboring. The same letter cell may not be used more than once in a word.

Time O(N * 4 * 3^(L-1)), N is number of cells in the board, L is maximum word length
Space O(M), M is number of letters in all words (size of trie)

```python
class Solution:
    _IS_WORD = "#"
    _USED = "$"
    DIRECTIONS = [(0, 1), (0, -1), (1, 0), (-1, 0)]
    def __init__(self):
        self.board = None
        self.found_words = None
        
    def constructTrie(self, words: List[str]) -> dict:
        root = {}
        for word in words:
            cur_node = root
            for letter in word:
                cur_node = cur_node.setdefault(letter, {})
            cur_node[self._IS_WORD] = word
        return root
    
    def _backtrack(self, row: int, col: int, parent: dict) -> None:
        if self._out_of_bound(row, col) or self.board[row][col] not in parent:
            return
        child = parent[self.board[row][col]]
        original_letter = self.board[row][col]
        self.board[row][col] = self._USED
        if self._IS_WORD in child:
            self.found_words.append(child[self._IS_WORD])
            del child[self._IS_WORD] # remove a word once found
        for dr, dc in self.DIRECTIONS:
            self._backtrack(row + dr, col + dc, child)
        self.board[row][col] = original_letter
        if not child: # prune leaf after visiting it
            parent.pop(self.board[row][col], None)
        
    def _out_of_bound(self, row: int, col: int) -> bool:
        return (row < 0 or row >= len(self.board) or 
                col < 0 or col >= len(self.board[row]))
        
    def findWords(self, board: List[List[str]], words: List[str]) -> List[str]:
        self.board, self.found_words = board, []
        m, n = len(board), len(board[0])
        root = self.constructTrie(words)
        for row in range(m):
            for col in range(n):
                self._backtrack(row, col, root)
        return self.found_words
```

### 51. N-Queens
### 52. N-Queens II <!-- {docsify-ignore} -->
> The n-queens puzzle is the problem of placing n queens on an n x n chessboard such that no two queens attack each other.
> 
> Given an integer n, return all distinct solutions to the n-queens puzzle.
> 
> Each solution contains a distinct board configuration of the n-queens' placement, where 'Q' and '.' both indicate a queen and an empty space, respectively.

Time O(N!) Space O(N)

```python
class Solution:
    
    def _backtrack(self, 
                   n: int, 
                   solution: List[int], 
                   solutions: List[List[str]]) -> None:
        if len(solution) == n:
            solutions.append(self._construct_board(n, solution))
            return
        for new_col in range(n):
            if self._can_place(new_col, solution):
                solution.append(new_col)
                self._backtrack(n, solution, solutions)
                solution.pop()
                    
    def _can_place(self, new_col: int, existing_cols: List[int]) -> bool:
        new_row = len(existing_cols)
        if new_col in existing_cols:
            return False
        for row, col in enumerate(existing_cols):
            if abs(new_row - row) == abs(new_col - col):
                return False
        return True
    
    def _construct_board(self, n, queen_cols: List[int]) -> List[str]:
        board = []
        for col in queen_cols:
            row_string = "." * col + "Q" + "." * (n - col - 1)
            board.append(row_string)
        return board
                    
    def solveNQueens(self, n: int) -> List[List[str]]:
        solutions = []
        self._backtrack(n, [], solutions)
        return solutions
```

### 489. Robot Room Cleaner
> Given a robot cleaner in a room modeled as a grid.
> 
> Each cell in the grid can be empty or blocked.
> 
> The robot cleaner with 4 given APIs can move forward, turn left or turn right. Each turn it made is 90 degrees.
> 
> When it tries to move into a blocked cell, its bumper sensor detects the obstacle and it stays on the current cell.
> 
> Design an algorithm to clean the entire room using only the 4 given APIs shown below.
> ```java
> interface Robot {
>   // returns true if next cell is open and robot moves into the cell.
>   // returns false if next cell is obstacle and robot stays on the current cell.
>   boolean move();
> 
>   // Robot will stay on the same cell after calling turnLeft/turnRight.
>   // Each turn will be 90 degrees.
>   void turnLeft();
>   void turnRight();
> 
>   // Clean the current cell.
>   void clean();
> }
> ```

Key thoughts:
1. How to maintain a visited set?   
   - Store (row, col), keep track of direction and coordinates.
2. How to go back after exploring one step?     
   - Turn 180 degrees, move, and turn 180 degrees.   
   - When we explore all 4 directions, it is important that in the end we are at the same direction as before.

```python
class Solution:
    _DIRECTIONS = [(0, 1), (1, 0), (0, -1), (-1, 0)]
    _NUM_DIRECTION = 4
    
    def __init__(self):
        self.robot = None
        self.visited = set()
    
    def _backtrack(self, row: int, col: int, pre_direction: int) -> None:
        self.visited.add((row, col))
        self.robot.clean()
        for i in range(4):
            cur_direction = (pre_direction + i) % self._NUM_DIRECTION
            dr, dc = self._DIRECTIONS[cur_direction]
            next_row, next_col = row + dr, col + dc
            if (next_row, next_col) not in self.visited and self.robot.move():
                self._backtrack(next_row, next_col, cur_direction)
                self._go_back()
            self.robot.turnRight() # It is crutial that we turn a total of 4 times
            
    def _go_back(self) -> None:
        self.robot.turnRight()
        self.robot.turnRight()
        self.robot.move()
        self.robot.turnRight()
        self.robot.turnRight()
        
    def cleanRoom(self, robot):
        """
        :type robot: Robot
        :rtype: None
        """
        self.robot = robot
        self._backtrack(0, 0, 0)
```

### 37. Sudoku Solver
> Write a program to solve a Sudoku puzzle by filling the empty cells.
> 
> A sudoku solution must satisfy all of the following rules:
> 
> Each of the digits 1-9 must occur exactly once in each row.
> Each of the digits 1-9 must occur exactly once in each column.
> Each of the digits 1-9 must occur exactly once in each of the 9 3x3 sub-boxes of the grid.
> The '.' character indicates empty cells.

It is crucial to end backtrack right when the Sudoku is solved, in order to preserve the solution.

```python
class Solution:
    BOARD_SIZE = 9
    BLOCK_SIZE = 3
    EMPTY = "."
    
    def __init__(self):
        self.board = None
        self.solved = False
        self.rows = [set() for i in range(self.BOARD_SIZE)]
        self.cols = [set() for i in range(self.BOARD_SIZE)]
        self.blocks = [set() for i in range(self.BOARD_SIZE)]
        
    def solveSudoku(self, board: List[List[str]]) -> None:
        """
        Do not return anything, modify board in-place instead.
        """
        self.board = board
        for row in range(self.BOARD_SIZE):
            for col in range(self.BOARD_SIZE):
                if board[row][col] != self.EMPTY:
                    num = int(board[row][col])
                    block = self.get_block_index(row, col)
                    self.place_number(row, col, block, num)
        self.backtrack(0, 0)
    
    def get_block_index(self, row: int, col: int) -> int:
        return (row // self.BLOCK_SIZE * self.BLOCK_SIZE + 
                col // self.BLOCK_SIZE)
    
    def place_number(self, row: int, col: int, block: int, num: int) -> None:
        self.rows[row].add(num)
        self.cols[col].add(num)
        self.blocks[block].add(num)
        self.board[row][col] = str(num)
        
    def remove_number(self, row: int, col: int, block: int, num: int) -> None:
        self.rows[row].remove(num)
        self.cols[col].remove(num)
        self.blocks[block].remove(num)
        self.board[row][col] = self.EMPTY
                    
    def backtrack(self, row, col) -> None:
        if row >= self.BOARD_SIZE:
            self.solved = True
            return
        if col == self.BOARD_SIZE - 1:
            next_row, next_col = row + 1, 0
        else:
            next_row, next_col = row, col + 1
        if self.board[row][col] != self.EMPTY:
            self.backtrack(next_row, next_col)
            return
        block = self.get_block_index(row, col)
        for num in range(1, self.BOARD_SIZE + 1):
            if (num not in self.rows[row] and 
                num not in self.cols[col] and 
                num not in self.blocks[block]):
                self.place_number(row, col, block, num)
                self.backtrack(next_row, next_col)
                if self.solved: # THIS IS IMPORTANT!!!!
                    return
                self.remove_number(row, col, block, num)
```
