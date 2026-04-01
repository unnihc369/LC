# Two Sum

Given an array `nums` and integer `target`, return indices of two numbers such that they add up to `target`.

## Approach 1: Brute Force
- Check all pairs.
- Return first pair that sums to target.
- Time: `O(n^2)`, Space: `O(1)`.

### Java
```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        for (int i = 0; i < nums.length; i++) {
            for (int j = i + 1; j < nums.length; j++) {
                if (nums[i] + nums[j] == target) {
                    return new int[]{i, j};
                }
            }
        }
        return new int[]{-1, -1};
    }
}
```

### C++
```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        for (int i = 0; i < (int)nums.size(); i++) {
            for (int j = i + 1; j < (int)nums.size(); j++) {
                if (nums[i] + nums[j] == target) return {i, j};
            }
        }
        return {-1, -1};
    }
};
```

## Approach 2: Better (Sort + Two Pointers)
- Store `(value, index)` pairs, sort by value.
- Use two pointers to find target sum.
- Time: `O(n log n)`, Space: `O(n)`.

### Java
```java
import java.util.Arrays;

class Solution {
    public int[] twoSum(int[] nums, int target) {
        int n = nums.length;
        int[][] arr = new int[n][2];
        for (int i = 0; i < n; i++) {
            arr[i][0] = nums[i];
            arr[i][1] = i;
        }
        Arrays.sort(arr, (a, b) -> Integer.compare(a[0], b[0]));
        int l = 0, r = n - 1;
        while (l < r) {
            int sum = arr[l][0] + arr[r][0];
            if (sum == target) return new int[]{arr[l][1], arr[r][1]};
            if (sum < target) l++;
            else r--;
        }
        return new int[]{-1, -1};
    }
}
```

### C++
```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        vector<pair<int, int>> arr;
        for (int i = 0; i < (int)nums.size(); i++) arr.push_back({nums[i], i});
        sort(arr.begin(), arr.end());
        int l = 0, r = (int)arr.size() - 1;
        while (l < r) {
            int sum = arr[l].first + arr[r].first;
            if (sum == target) return {arr[l].second, arr[r].second};
            if (sum < target) l++;
            else r--;
        }
        return {-1, -1};
    }
};
```

## Approach 3: Optimal (Hash Map)
- For each number `x`, check if `target - x` already seen.
- If yes, return previous index and current index.
- Time: `O(n)` average, Space: `O(n)`.

### Java
```java
import java.util.HashMap;
import java.util.Map;

class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> mp = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            int need = target - nums[i];
            if (mp.containsKey(need)) return new int[]{mp.get(need), i};
            mp.put(nums[i], i);
        }
        return new int[]{-1, -1};
    }
}
```

### C++
```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> mp;
        for (int i = 0; i < (int)nums.size(); i++) {
            int need = target - nums[i];
            if (mp.count(need)) return {mp[need], i};
            mp[nums[i]] = i;
        }
        return {-1, -1};
    }
};
```
