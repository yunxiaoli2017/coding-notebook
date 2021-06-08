------

### 3. Longest Substring Without Repeating Characters

> Given a string s, find the length of the longest substring without repeating characters.

When we encounter a repeating character during iteration, what would we want to know? 

- The position where that character last occured. Then we will discard it and all its previous characters.

<!-- tabs:start -->

#### **Java**

```java
public int lengthOfLongestSubstring(String s) {
  if (s.length() < 1) {
    return 0;
  }
  Map<Character, Integer> lastOccurMap = new HashMap<>();
  int maxLength = 0;
  int startAt = 0;
  for (int i = 0; i < s.length(); i++) {
    int lastOccurAt = lastOccurMap.getOrDefault(s.charAt(i), -1);
    if (startAt <= lastOccurAt) {
      maxLength = Math.max(maxLength, i - startAt);
      startAt = lastOccurAt + 1;
    }
    lastOccurMap.put(s.charAt(i), i);
  }
  maxLength = Math.max(maxLength, s.length() - startAt);
  return maxLength;
}
```

#### **Python**

```python
def lengthOfLongestSubstring(self, s):
    """
    :type s: str
    :rtype: int
    """
    last_occur_at = {}
    max_length = 0
    start = 0
    for i in range(len(s)):
      if s[i] in last_occur_at and last_occur_at[s[i]] >= start:
        max_length = max(max_length, i - start)
        start = last_occur_at[s[i]] + 1
      last_occur_at[s[i]] = i
    max_length = max(max_length, len(s) - start)
    return max_length
```

#### **C++**

```cpp
int lengthOfLongestSubstring(string s) {
  unordered_map<char, int> lastOccurAt;
  int start = 0, maxLength = 0;
  for (int i = 0; i < s.length(); i++) {
    if (lastOccurAt.find(s[i]) != lastOccurAt.end() && lastOccurAt[s[i]] >= start) {
      maxLength = max(maxLength, i - start);
      start = lastOccurAt[s[i]] + 1;
    }
    lastOccurAt[s[i]] = i;
  }
  maxLength = max(maxLength, (int) s.length() - start);
  return maxLength;
}
```

<!-- tabs:end -->
