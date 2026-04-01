# Group Anagrams

Given an array of strings `strs`, group the anagrams together.

## Approach 1: Brute Force
- Try to place each string into an existing group by checking anagram with group representative.
- Anagram check uses frequency array.
- Time: roughly `O(n^2 * k)`, Space: `O(n*k)`.

### Java
```java
import java.util.*;

class Solution {
    private boolean isAnagram(String a, String b) {
        if (a.length() != b.length()) return false;
        int[] f = new int[26];
        for (char c : a.toCharArray()) f[c - 'a']++;
        for (char c : b.toCharArray()) f[c - 'a']--;
        for (int x : f) if (x != 0) return false;
        return true;
    }

    public List<List<String>> groupAnagrams(String[] strs) {
        List<List<String>> ans = new ArrayList<>();
        for (String s : strs) {
            boolean placed = false;
            for (List<String> g : ans) {
                if (isAnagram(s, g.get(0))) {
                    g.add(s);
                    placed = true;
                    break;
                }
            }
            if (!placed) {
                List<String> ng = new ArrayList<>();
                ng.add(s);
                ans.add(ng);
            }
        }
        return ans;
    }
}
```

### C++
```cpp
class Solution {
    bool isAnagram(const string& a, const string& b) {
        if (a.size() != b.size()) return false;
        vector<int> f(26, 0);
        for (char c : a) f[c - 'a']++;
        for (char c : b) f[c - 'a']--;
        for (int x : f) if (x != 0) return false;
        return true;
    }
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        vector<vector<string>> ans;
        for (string& s : strs) {
            bool placed = false;
            for (auto& g : ans) {
                if (isAnagram(s, g[0])) {
                    g.push_back(s);
                    placed = true;
                    break;
                }
            }
            if (!placed) ans.push_back({s});
        }
        return ans;
    }
};
```

## Approach 2: Better (Sorted Key)
- Sort each string, use sorted string as map key.
- All anagrams share same sorted key.
- Time: `O(n * k log k)`, Space: `O(n*k)`.

### Java
```java
import java.util.*;

class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        Map<String, List<String>> mp = new HashMap<>();
        for (String s : strs) {
            char[] ch = s.toCharArray();
            Arrays.sort(ch);
            String key = new String(ch);
            mp.computeIfAbsent(key, z -> new ArrayList<>()).add(s);
        }
        return new ArrayList<>(mp.values());
    }
}
```

### C++
```cpp
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        unordered_map<string, vector<string>> mp;
        for (string s : strs) {
            string key = s;
            sort(key.begin(), key.end());
            mp[key].push_back(s);
        }
        vector<vector<string>> ans;
        for (auto& it : mp) ans.push_back(it.second);
        return ans;
    }
};
```

## Approach 3: Optimal (Frequency Signature Key)
- Build a frequency-based signature like `#1#0#2...` for 26 letters.
- Avoid per-string sorting.
- Time: `O(n*k)`, Space: `O(n*k)`.

### Java
```java
import java.util.*;

class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        Map<String, List<String>> mp = new HashMap<>();
        for (String s : strs) {
            int[] cnt = new int[26];
            for (char c : s.toCharArray()) cnt[c - 'a']++;
            StringBuilder key = new StringBuilder();
            for (int x : cnt) key.append('#').append(x);
            mp.computeIfAbsent(key.toString(), z -> new ArrayList<>()).add(s);
        }
        return new ArrayList<>(mp.values());
    }
}
```

### C++
```cpp
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        unordered_map<string, vector<string>> mp;
        for (string& s : strs) {
            vector<int> cnt(26, 0);
            for (char c : s) cnt[c - 'a']++;
            string key;
            for (int x : cnt) key += "#" + to_string(x);
            mp[key].push_back(s);
        }
        vector<vector<string>> ans;
        for (auto& it : mp) ans.push_back(it.second);
        return ans;
    }
};
```
