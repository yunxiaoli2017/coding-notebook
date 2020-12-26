------

### 1046. Last Stone Weight

> We have a collection of stones, each stone has a positive integer weight.
> 
> Each turn, we choose the **two heaviest stones** and smash them together.  Suppose the stones have weights x and y with x <= y.  The result of this smash is:
> 
> - If x == y, both stones are totally destroyed;
> - If x != y, the stone of weight x is totally destroyed, and the stone of weight y has new weight y-x.  
>
> At the end, there is at most 1 stone left.  Return the weight of this stone (or 0 if there are no stones left.)


- Use `PriorityQueue` to store weights, poll two stones each time and collapse.  
Time O(NlogN) Space O(N)  
- `Count Sort`, use an array to count number of stones of each weight value. Then iterate from heaviest weight and collapse.  
Time O(N + W) Space O(W) where W is the maximum weight of stones


### 1049. Last Stone Weight II

> We have a collection of rocks, each rock has a positive integer weight.
> 
> Each turn, we choose **any two rocks** and smash them together.  Suppose the stones have weights x and y with x <= y.  The result of this smash is:
> 
> - If x == y, both stones are totally destroyed;
> - If x != y, the stone of weight x is totally destroyed, and the stone of weight y has new weight y-x.
> 
> At the end, there is at most 1 stone left.  Return the **smallest possible weight** of this stone (the weight is 0 if there are no stones left.)

It's like two armies fighting against each other. If two armies have the same number of soldiers, then they all die; If one army has more soldiers, then those extra soldiers can survive, for subsequent wars.  
Here equivalently we want the war to be as cruel as possible, that is, let there be as few as possible people surviving in the end.  
Then we need to have a as balanced as possible setup; Two armies should have minimum difference in terms of number of soldiers. Now the problem becomes:  

**Divide the stones into two groups, such that their sums have minimum difference.**
- Why rebellion is not necessary? Repellion: suppose stone A = 7, beats stone B = 3, and becomes A = 4; Now should it return its original troop, or should be rebel and join troop of B? It turns out that, such rebellion is equivalent to that A and B switches troops from the beginning. So considering rebellion is not necessary.

Let S be the total weights, S1 and S2 be the group weights (S1 + S2 == S), and S1 >= S2  
We want to minimize S1 - S2 = S - 2 * S2; Equivalently we want to maximize S2, with the constriant that S2 <= S / 2  
