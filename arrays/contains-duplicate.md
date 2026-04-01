# Contains Duplicate

Given an integer array `nums`, return `true` if any value appears at least twice, otherwise return `false`.

## Approach 1: Brute Force
- Compare every pair `(i, j)` where `i < j`.
- If `nums[i] == nums[j]`, duplicate exists.
- Time: `O(n^2)`, Space: `O(1)`.

### Java
```java
class Solution {
    public boolean containsDuplicate(int[] nums) {
        for (int i = 0; i < nums.length; i++) {
            for (int j = i + 1; j < nums.length; j++) {
                if (nums[i] == nums[j]) return true;
            }
        }
        return false;
    }
}
```

### C++
```cpp
class Solution {
public:
    bool containsDuplicate(vector<int>& nums) {
        for (int i = 0; i < (int)nums.size(); i++) {
            for (int j = i + 1; j < (int)nums.size(); j++) {
                if (nums[i] == nums[j]) return true;
            }
        }
        return false;
    }
};
```

## Approach 2: Better (Sort + Scan)
- Sort the array.
- Any duplicate values become adjacent.
- Time: `O(n log n)`, Space: `O(1)` extra (ignoring sort internals).

### Java
```java
import java.util.Arrays;

class Solution {
    public boolean containsDuplicate(int[] nums) {
        Arrays.sort(nums);
        for (int i = 1; i < nums.length; i++) {
            if (nums[i] == nums[i - 1]) return true;
        }
        return false;
    }
}
```

### C++
```cpp
class Solution {
public:
    bool containsDuplicate(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        for (int i = 1; i < (int)nums.size(); i++) {
            if (nums[i] == nums[i - 1]) return true;
        }
        return false;
    }
};
```

## Approach 3: Optimal (Hash Set)
- Insert elements into a hash set while traversing.
- If an element already exists in set, duplicate found.
- Time: `O(n)` average, Space: `O(n)`.

### Java
```java
import java.util.HashSet;
import java.util.Set;

class Solution {
    public boolean containsDuplicate(int[] nums) {
        Set<Integer> seen = new HashSet<>();
        for (int x : nums) {
            if (!seen.add(x)) return true;
        }
        return false;
    }
}
```

### C++
```cpp
class Solution {
public:
    bool containsDuplicate(vector<int>& nums) {
        unordered_set<int> seen;
        for (int x : nums) {
            if (seen.count(x)) return true;
            seen.insert(x);
        }
        return false;
    }
};
```
