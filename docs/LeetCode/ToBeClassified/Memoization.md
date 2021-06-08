------

### 877. Stone Game
> Alex and Lee play a game with piles of stones. There are an even number of piles arranged in a row, and each pile has a positive integer number of stones piles[i].
> 
> The objective of the game is to end with the most stones.  The total number of stones is odd, so there are no ties.
> 
> Alex and Lee take turns, with **Alex starting first**.  Each turn, a player takes the entire pile of stones from either the beginning or the end of the row.  This continues until there are no more piles left, at which point the person with the most stones wins.
> 
> Assuming Alex and Lee play optimally, return True if and only if Alex wins the game.

Alex can always win.    
Alex can take all stones with evene indices, or all stones with odd indices. One of these two choices must be larger.

```java
public boolean stoneGame(int[] piles) {
  return true;
}
```

### 1140. Stone Game II
> Alice and Bob continue their games with piles of stones.  There are a number of piles arranged in a row, and each pile has a positive integer number of stones piles[i].  The objective of the game is to end with the most stones. 
> 
> **Alice and Bob take turns, with Alice starting first.  Initially, M = 1.**
> 
> **On each player's turn, that player can take all the stones in the first X remaining piles, where 1 <= X <= 2M.  Then, we set M = max(M, X).**
> 
> The game continues until all the stones have been taken.
> 
> Assuming Alice and Bob play optimally, return the maximum number of stones Alice can get.

```java
public int stoneGameII(int[] piles) {
  int[] suffixSum = new int[piles.length];
  suffixSum[piles.length - 1] = piles[piles.length - 1];
  for (int i = piles.length - 2; i >= 0; i--) {
    suffixSum[i] = piles[i] + suffixSum[i + 1];
  }
  int[][] memo = new int[piles.length][piles.length];
  return helper(piles, suffixSum, 0, 1, memo);
}
// return maximum number of stones Alice can get, when the current stone is of index cur, and when current M is M. This answer is stored in memo after computed.
private int helper(int[] piles, int[] suffixSum, int cur, int M, int[][] memo) {
  if (cur >= piles.length) {
    return 0;
  }
  // When Alice can take all the rest, just take them.
  if (2 * M >= piles.length - cur) {
    return suffixSum[cur];
  }
  if (memo[cur][M] > 0) {
    return memo[cur][M];
  }
  // Try out all options and update to the optimal answer.
  for (int X = 1; X <= 2 * M; X++) {
    memo[cur][M] = Math.max(memo[cur][M], suffixSum[cur] - helper(piles, suffixSum, cur + X, Math.max(X, M), memo));
  }
  return memo[cur][M];
}
```

### 1406. Stone Game III
> Alice and Bob continue their games with piles of stones. There are several stones arranged in a row, and each stone has an associated value which is an integer given in the array stoneValue.
> 
> **Alice and Bob take turns, with Alice starting first. On each player's turn, that player can take 1, 2 or 3 stones from the first remaining stones in the row.**
> 
> The score of each player is the sum of values of the stones taken. The score of each player is 0 initially.
> 
> The objective of the game is to end with the highest score, and the winner is the player with the highest score and there could be a tie. The game continues until all the stones have been taken.
> 
> Assume Alice and Bob play optimally.
> 
> Return "Alice" if Alice will win, "Bob" if Bob will win or "Tie" if they end the game with the same score.

```java
// space-optimized dp. Time O(n) Space O(1)
// Let dp[i] represent the maximum advantage one can obtain if he starts his turn when the current stone is i
// induction rule:
// dp[i] = max(s[i] - dp[i+1], s[i] + s[i+1] - dp[i+2], s[i] + s[i+1] + s[i+2] - dp[i+3])
public String stoneGameIII(int[] stoneValue) {
  int n = stoneValue.length;
  int[] dp = new int[4];
  for (int i = n - 1; i >= 0; i--) {
    dp[0] = Integer.MIN_VALUE;
    int thisTake = 0;
    for (int j = 0; i + j < n && j < 3; j++) {
      thisTake += stoneValue[i + j];
      dp[0] = Math.max(dp[0], thisTake - dp[j + 1]);
    }
    for (int j = dp.length - 1; j > 0; j--) {
      dp[j] = dp[j - 1];
    }
  }
  if (dp[0] > 0) return "Alice";
  else if (dp[0] < 0) return "Bob";
  else return "Tie";
}
```

### 1510. Stone Game IV
> Alice and Bob take turns playing a game, with Alice starting first.
> 
> Initially, there are n stones in a pile.  On each player's turn, that player makes a move consisting of removing any non-zero square number of stones in the pile.
> 
> Also, if a player cannot make a move, he/she loses the game.
> 
> Given a positive integer n. Return True if and only if Alice wins the game otherwise return False, assuming both players play optimally.

```java
// dp[i] represents if the first player can win when starting with i stones.
// dp[0] = false; dp[1] = true; 
// induction rule:
// dp[i] = !dp[i - 1] || !dp[i - 4] || ...
// if any dp[i - squareNum] is false, then dp[i] is true
// if all dp[i - squareNum] is true, then dp[i] is false
// if we evaluate all dp[i - squareNum] for all i, then time complexity O(n^(3/2)) Space O(n)
public boolean winnerSquareGame(int n) {
  boolean[] dp = new boolean[n + 1];
  int numSquareNumbers = (int) Math.sqrt(n);
  int[] squareNumbers = new int[numSquareNumbers];
  for (int i = 1; i <= numSquareNumbers; i++) {
    squareNumbers[i - 1] = i * i;
  }
  for (int i = 1; i <= n; i++) {
    for (int j = 0; j < numSquareNumbers && squareNumbers[j] <= i; j++) {
      if (!dp[i - squareNumbers[j]]) {
        dp[i] = true;
        break;
      }
    }
  }
  return dp[n];
}
```
