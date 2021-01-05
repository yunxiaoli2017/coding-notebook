------

Store key info with hashtable, and refer to previous values when sliding

## CumSum

### 325. Maximum Size Subarray Sum Equals k
> Given an array nums and a target value k, find the maximum length of a subarray that sums to k. If there isn't one, return 0 instead.

Sliding and storing first-occurred cumSum (skip if already present in the map).

### 523. Continuous Subarray Sum that sums up to a multiple of k (corner case k = 0) <!-- {docsify-ignore} -->
> Given a list of non-negative numbers and a target integer k, write a function to check if the array has a continuous subarray of size at least 2 that sums up to a multiple of k, that is, sums up to n*k where n is also an integer.
### 974. Subarray Sums Divisible by K (return number of subarrys) <!-- {docsify-ignore} -->
> Given an array A of integers, return the number of (contiguous, non-empty) subarrays that have a sum divisible by K.

Sliding and storing (cumSum % k or cumSum if k == 0)

### 525. Contiguous Array <!-- {docsify-ignore} -->
> Given a binary array, find the maximum length of a contiguous subarray with equal numbers of 0 and 1.

Sliding and storing (No. of 1s - No. of 0s)


```java
class Solution {
  // 325. Maximum Size Subarray Sum Equals k
  public int maxSubArrayLen(int[] nums, int k) {
    Map<Integer, Integer> firstOccur = new HashMap<>();
    firstOccur.put(0, -1);
    int cumSum = 0;
    int maxLen = 0;
    for (int i = 0; i < nums.length; i++) {
      cumSum += nums[i];
      Integer index = firstOccur.get(cumSum - k);
      if (index != null) {
        maxLen = Math.max(maxLen, i - index);
      }
      firstOccur.putIfAbsent(cumSum, i);
    }
    return maxLen;
  }
}
```

### 1371. Find the Longest Substring Containing Vowels in Even Counts
> Given the string s, return the size of the longest substring containing each vowel an even number of times. That is, 'a', 'e', 'i', 'o', and 'u' must appear an even number of times.

A harder version with bit manipulation needed. (Or other hashing techniques)

```python
class Solution:
    _BIT_BY_VOWEL = {'a': 1, 'e': 2, 'i': 4, 'o': 8, 'u': 16}
    # Time O(n) Space O(1)
    def findTheLongestSubstring(self, s: str) -> int:
        bits_by_index = {0: 0}
        max_len = 0
        bits = 0 # all is even initially
        for i in range(len(s)):
            if s[i] in self._BIT_BY_VOWEL:
                bits = bits ^ self._BIT_BY_VOWEL[s[i]]
            if bits not in bits_by_index:
                bits_by_index[bits] = i + 1
            else:
                max_len = max(max_len, i + 1 - bits_by_index[bits])
        return max_len
```

## Two pointers

### 3. Longest Substring Without Repeating Characters
> Given a string s, find the length of the longest substring without repeating characters.

Record the last occurence of a character; Move left pointer to right of last occurence when encountering an existing character in current window.
// Questions to ask: constraints on characters? ASCII? Alphabetic?

### 438. Find All Anagrams in a String
> Given a string s and a non-empty string p, find all the start indices of p's anagrams in s.
> 
> Strings consists of lowercase English letters only.

Sliding count array. Keep updating and comparing with the target count array.
Use an integer ```mismatch``` to record how many types of characters (of 26 types in total) have not been matched. Time O(s + t) Space O(1) (O(26))

### 249. Group Shifted Strings
> Given a string, we can "shift" each of its letter to its successive letter, for example: "abc" -> "bcd". We can keep "shifting" which forms the sequence:
> 
> "abc" -> "bcd" -> ... -> "xyz"
> Given a list of non-empty strings which contains only lowercase alphabets, group all strings that belong to the same shifting sequence.

Be careful about negative shifts; Be careful about % in Java.

```java
class Solution {
  public List<List<String>> groupStrings(String[] strings) {
    Map<String, List<Integer>> map = new HashMap<>();
    List<List<String>> res = new ArrayList<>();
    for (int i = 0; i < strings.length; i++) {
      String s = shift(strings[i]);
      List<Integer> indices = map.get(s);
      if (indices == null) {
        List<Integer> temp = new ArrayList<>();
        temp.add(i);
        map.put(s, temp);
      } else {
        indices.add(i);
      }
    }
    for (Map.Entry<String, List<Integer>> entry : map.entrySet()) {
      List<String> temp = new ArrayList<>();
      for (Integer i : entry.getValue()) {
        temp.add(strings[i]);
      }
      res.add(temp);
    }
    return res;
  }
  private String shift(String s) {
    StringBuilder sb = new StringBuilder();
    int offset = ('a' - s.charAt(0) + 26) % 26;
    for (int i = 0; i < s.length(); i++) {
      int j = (s.charAt(i) - 'a' + offset) % 26;
      // j = (j < 0) ? j + 26 : j;
      char c = (char) ('a' + j);
      sb.append(c);
    }
    return sb.toString();
  }
}
```

### 340. Longest Substring with At Most K Distinct Characters (hard, similar to 438)
> Given a string s and an integer k, return the length of the longest substring of s that contains at most k distinct characters.

Slide with two pointers like a worm and updating count map; Update ```num_distinct``` when count changes around 0; Move left pointer until ```num_distinct <= k```

### 76. Minimum Window Substring (Hard)
> Given a string S and a string T, find the minimum window in S which will contain all the characters in T in complexity O(n).

Slide with two pointers like a worm and updating count map; Store character count in a hashmap; Update (Number of absent unique characters) when one of the counts changes around 0.

```java
class Solution {
  // Time O(|S| + |T|) Space O(K), K is number of unique characters in t
  public String minWindow(String s, String t) {
    if (s.length() < t.length()) return "";
    Map<Character, Integer> count = new HashMap<>();
    for (int i = 0; i < t.length(); i++) {
      char c = t.charAt(i);
      count.put(c, count.getOrDefault(c, 0) + 1);
    }
    int absent = count.size();
    int i = 0, j = 0;
    int minI = 0, minJ = 0;
    int minLen = Integer.MAX_VALUE;
    while (j < s.length()) {
      while (j < s.length() && absent > 0) {
        char c = s.charAt(j);
        Integer num = count.get(c);
        if (num != null) {
          count.put(c, num - 1);
          if (num == 1) absent--;
        }
        j++;
      }
      boolean flag = (absent == 0);
      while (i < j && absent == 0) {
        char c = s.charAt(i);
        Integer num = count.get(c);
        if (num != null) {
          count.put(c, num + 1);
          if (num == 0) absent++;
        }
        i++;
      }
      if (flag == true && j - i + 1 < minLen) {
        minI = i - 1;
        minJ = j;
        minLen = j - i + 1;
      }
    }
    if (minLen == Integer.MAX_VALUE) return "";
    return s.substring(minI, minJ);
  }
}
```
