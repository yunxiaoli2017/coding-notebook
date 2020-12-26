------
### Stock Problems
> 121. Best Time to Buy and Sell Stock (can hold multiple stocks)  

> 122. Best Time to Buy and Sell Stock II (can hold at most 1 stock)  

> 123. Best Time to Buy and Sell Stock III (at most 2 transactions)  

> 188. Best Time to Buy and Sell Stock IV **(at most k transactions)**   

> 309. Best Time to Buy and Sell Stock with Cooldown **(one-day-cooldown)**   

> 714. Best Time to Buy and Sell Stock with **Transaction Fee** 

Let T[i][k][0(1)] denote maximum profit at day i, with at most k transactions, with no(one) stock at hand. 
Base cases:  
> T[-1][k][0] = 0, T[-1][k][1] = -Infinity  
> T[i][0][0] = 0, T[i][0][1] = -Infinity

Recurrence relation:  
> T[i][k][0] = max(T[i-1][k][0], T[i-1][k][1] + prices[i])  
> T[i][k][1] = max(T[i-1][k][1], T[i-1][k-1][0] - prices[i])

With an arbitrary k:
```java
public int maxProfit(int k, int[] prices) {
  if (k >= prices.length >>> 1) {
    int T_ik0 = 0, T_ik1 = Integer.MIN_VALUE;
    for (int price : prices) {
      int T_ik0_old = T_ik0;
      T_ik0 = Math.max(T_ik0, T_ik1 + price);
      T_ik1 = Math.max(T_ik1, T_ik0_old - price);
    }
    return T_ik0;
  }  
  int[] T_ik0 = new int[k + 1];
  int[] T_ik1 = new int[k + 1];
  Arrays.fill(T_ik1, Integer.MIN_VALUE);
  for (int price : prices) {
    for (int j = k; j > 0; j--) { // why not include 0? No transaction, no cost or profit.
      T_ik0[j] = Math.max(T_ik0[j], T_ik1[j] + price);
      T_ik1[j] = Math.max(T_ik1[j], T_ik0[j - 1] - price);
    }
  }
  return T_ik0[k];
}
```

**Follow-up**:        max profit with **exactly** k transactions.  
**Solution**:         Change initializations on vacant[k + 1] array.   
**Caution**:          When inducting, watch out for overflow by Integer.MIN_VALUE - price.
> Arrays.fill(vacant, Integer.MIN_VALUE);   
> vacant[0] = 0;    

In practice, dimension for [i] can be removed to optimize space. Because dp[i] here only depends on previous value; Or prePre value if there is one-day-cooldown.  
With **one-day-cooldown**:
> T[i][k][0] = max(T[i-1][k][0], T[i-1][k][1] + prices[i])  
> T[i][k][1] = max(T[i-1][k][1], T[i-2][k][0] - prices[i])
```java
public int maxProfit(int[] prices) {
    int T_ik0_pre = 0, T_ik0 = 0, T_ik1 = Integer.MIN_VALUE;
    
    for (int price : prices) {
        int T_ik0_old = T_ik0; // save yesterday's value before overwriting with today's value
        T_ik0 = Math.max(T_ik0, T_ik1 + price);
        T_ik1 = Math.max(T_ik1, T_ik0_pre - price);
        T_ik0_pre = T_ik0_old; // pass yesterday's value to tomorrow
    }
    
    return T_ik0;
}
```
My version with **one-day-cooldown**:
```java
public int maxProfit(int[] prices) {
    int vacantCur = 0, heldCur = Integer.MIN_VALUE;
    int vacantPre = 0, vacantPrePre = 0;
    for (int price : prices) {
        vacantPrePre = vacantPre; // the day before yesterday
        vacantPre = vacantCur; // yesterday
        vacantCur = Math.max(vacantCur, heldCur + price); // today
        heldCur = Math.max(heldCur, vacantPrePre - price); // must be vacant the day before yesterday to sell
    }
    
    return vacantCur;
}
```
What if with **two-day-cooldown**?
```java
public int maxProfit(int[] prices) {
    int vacantCur = 0, heldCur = Integer.MIN_VALUE;
    int vacantPre = 0, vacantPrePre = 0, vacantPrePrePre = 0;
    for (int price : prices) {
        vacantPrePrePre = vacantPrePre; // the day before the day before yesterday
        vacantPrePre = vacantPre; // the day before yesterday
        vacantPre = vacantCur; // yesterday
        vacantCur = Math.max(vacantCur, heldCur + price); // today
        heldCur = Math.max(heldCur, vacantPrePrePre - price); // must be vacant the day before the day before yesterday to sell
    }
    
    return vacantCur;
}
```
