# Longest Consecutive Sequence

Given an unsorted integer array, return the length of the longest consecutive elements sequence.

## Approach 1: Brute Force
- For each number `x`, keep checking if `x+1`, `x+2`, ... exist using linear search.
- Time: `O(n^3)` worst case with naive search, Space: `O(1)`.

### Java
```java
class Solution {
    private boolean exists(int[] nums, int target) {
        for (int x : nums) if (x == target) return true;
        return false;
    }

    public int longestConsecutive(int[] nums) {
        int best = 0;
        for (int x : nums) {
            int len = 1;
            int cur = x;
            while (exists(nums, cur + 1)) {
                cur++;
                len++;
            }
            best = Math.max(best, len);
        }
        return best;
    }
}
```

### C++
```cpp
class Solution {
    bool exists(vector<int>& nums, int target) {
        for (int x : nums) if (x == target) return true;
        return false;
    }
public:
    int longestConsecutive(vector<int>& nums) {
        int best = 0;
        for (int x : nums) {
            int len = 1, cur = x;
            while (exists(nums, cur + 1)) {
                cur++;
                len++;
            }
            best = max(best, len);
        }
        return best;
    }
};
```

## Approach 2: Better (Sort)
- Sort array.
- Scan and count streak, skipping duplicates.
- Time: `O(n log n)`, Space: `O(1)` extra (ignoring sort internals).

### Java
```java
import java.util.Arrays;

class Solution {
    public int longestConsecutive(int[] nums) {
        if (nums.length == 0) return 0;
        Arrays.sort(nums);
        int best = 1, cur = 1;
        for (int i = 1; i < nums.length; i++) {
            if (nums[i] == nums[i - 1]) continue;
            if (nums[i] == nums[i - 1] + 1) cur++;
            else cur = 1;
            best = Math.max(best, cur);
        }
        return best;
    }
}
```

### C++
```cpp
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        if (nums.empty()) return 0;
        sort(nums.begin(), nums.end());
        int best = 1, cur = 1;
        for (int i = 1; i < (int)nums.size(); i++) {
            if (nums[i] == nums[i - 1]) continue;
            if (nums[i] == nums[i - 1] + 1) cur++;
            else cur = 1;
            best = max(best, cur);
        }
        return best;
    }
};
```

## Approach 3: Optimal (Hash Set Sequence Start)
- Insert all numbers into hash set.
- Only start counting from numbers where `x-1` is absent (start of sequence).
- Count forward once per sequence.
- Time: `O(n)` average, Space: `O(n)`.

### Java
```java
import java.util.HashSet;
import java.util.Set;

class Solution {
    public int longestConsecutive(int[] nums) {
        Set<Integer> st = new HashSet<>();
        for (int x : nums) st.add(x);
        int best = 0;
        for (int x : st) {
            if (!st.contains(x - 1)) {
                int cur = x, len = 1;
                while (st.contains(cur + 1)) {
                    cur++;
                    len++;
                }
                best = Math.max(best, len);
            }
        }
        return best;
    }
}
```

### C++
```cpp
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        unordered_set<int> st(nums.begin(), nums.end());
        int best = 0;
        for (int x : st) {
            if (!st.count(x - 1)) {
                int cur = x, len = 1;
                while (st.count(cur + 1)) {
                    cur++;
                    len++;
                }
                best = max(best, len);
            }
        }
        return best;
    }
};
```
