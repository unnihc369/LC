# Valid Anagram

Given two strings `s` and `t`, return `true` if `t` is an anagram of `s`.

## Approach 1: Brute Force
- If lengths differ, return `false`.
- For each character in `s`, find and remove one matching character from `t`.
- Time: `O(n^2)`, Space: `O(n)` (mutable copy).

### Java
```java
class Solution {
    public boolean isAnagram(String s, String t) {
        if (s.length() != t.length()) return false;
        StringBuilder sb = new StringBuilder(t);
        for (char c : s.toCharArray()) {
            int idx = sb.indexOf(String.valueOf(c));
            if (idx == -1) return false;
            sb.deleteCharAt(idx);
        }
        return true;
    }
}
```

### C++
```cpp
class Solution {
public:
    bool isAnagram(string s, string t) {
        if (s.size() != t.size()) return false;
        for (char c : s) {
            size_t pos = t.find(c);
            if (pos == string::npos) return false;
            t.erase(pos, 1);
        }
        return true;
    }
};
```

## Approach 2: Better (Sort)
- Sort both strings and compare.
- Time: `O(n log n)`, Space: `O(1)` extra (language sort internals ignored).

### Java
```java
import java.util.Arrays;

class Solution {
    public boolean isAnagram(String s, String t) {
        if (s.length() != t.length()) return false;
        char[] a = s.toCharArray();
        char[] b = t.toCharArray();
        Arrays.sort(a);
        Arrays.sort(b);
        return Arrays.equals(a, b);
    }
}
```

### C++
```cpp
class Solution {
public:
    bool isAnagram(string s, string t) {
        if (s.size() != t.size()) return false;
        sort(s.begin(), s.end());
        sort(t.begin(), t.end());
        return s == t;
    }
};
```

## Approach 3: Optimal (Frequency Count)
- Count frequency of letters in `s` and `t`.
- If all counts match, they are anagrams.
- Time: `O(n)`, Space: `O(1)` for fixed alphabet.

### Java
```java
class Solution {
    public boolean isAnagram(String s, String t) {
        if (s.length() != t.length()) return false;
        int[] freq = new int[26];
        for (char c : s.toCharArray()) freq[c - 'a']++;
        for (char c : t.toCharArray()) freq[c - 'a']--;
        for (int x : freq) if (x != 0) return false;
        return true;
    }
}
```

### C++
```cpp
class Solution {
public:
    bool isAnagram(string s, string t) {
        if (s.size() != t.size()) return false;
        vector<int> freq(26, 0);
        for (char c : s) freq[c - 'a']++;
        for (char c : t) freq[c - 'a']--;
        for (int x : freq) if (x != 0) return false;
        return true;
    }
};
```
