# Product of Array Except Self

Given `nums`, return `answer[i] = product(nums) / nums[i]` conceptually, but without using division in optimal approach.

## Approach 1: Brute Force
- For each index `i`, multiply all elements except `i`.
- Time: `O(n^2)`, Space: `O(1)` extra (excluding output).

### Java
```java
class Solution {
    public int[] productExceptSelf(int[] nums) {
        int n = nums.length;
        int[] ans = new int[n];
        for (int i = 0; i < n; i++) {
            int prod = 1;
            for (int j = 0; j < n; j++) {
                if (i != j) prod *= nums[j];
            }
            ans[i] = prod;
        }
        return ans;
    }
}
```

### C++
```cpp
class Solution {
public:
    vector<int> productExceptSelf(vector<int>& nums) {
        int n = nums.size();
        vector<int> ans(n);
        for (int i = 0; i < n; i++) {
            int prod = 1;
            for (int j = 0; j < n; j++) {
                if (i != j) prod *= nums[j];
            }
            ans[i] = prod;
        }
        return ans;
    }
};
```

## Approach 2: Better (Division with Zero Handling)
- Compute total product of non-zero values and count zeros.
- Use zero-count rules to fill output.
- Time: `O(n)`, Space: `O(1)` extra.

### Java
```java
class Solution {
    public int[] productExceptSelf(int[] nums) {
        int n = nums.length;
        int zeroCount = 0;
        int prod = 1;
        for (int x : nums) {
            if (x == 0) zeroCount++;
            else prod *= x;
        }
        int[] ans = new int[n];
        for (int i = 0; i < n; i++) {
            if (zeroCount > 1) ans[i] = 0;
            else if (zeroCount == 1) ans[i] = nums[i] == 0 ? prod : 0;
            else ans[i] = prod / nums[i];
        }
        return ans;
    }
}
```

### C++
```cpp
class Solution {
public:
    vector<int> productExceptSelf(vector<int>& nums) {
        int n = nums.size(), zeroCount = 0, prod = 1;
        for (int x : nums) {
            if (x == 0) zeroCount++;
            else prod *= x;
        }
        vector<int> ans(n, 0);
        for (int i = 0; i < n; i++) {
            if (zeroCount > 1) ans[i] = 0;
            else if (zeroCount == 1) ans[i] = nums[i] == 0 ? prod : 0;
            else ans[i] = prod / nums[i];
        }
        return ans;
    }
};
```

## Approach 3: Optimal (Prefix + Suffix Products)
- `ans[i]` first stores product of all elements left of `i`.
- Traverse from right keeping suffix product and multiply into `ans[i]`.
- No division used.
- Time: `O(n)`, Space: `O(1)` extra (excluding output).

### Java
```java
class Solution {
    public int[] productExceptSelf(int[] nums) {
        int n = nums.length;
        int[] ans = new int[n];
        ans[0] = 1;
        for (int i = 1; i < n; i++) ans[i] = ans[i - 1] * nums[i - 1];

        int suffix = 1;
        for (int i = n - 1; i >= 0; i--) {
            ans[i] *= suffix;
            suffix *= nums[i];
        }
        return ans;
    }
}
```

### C++
```cpp
class Solution {
public:
    vector<int> productExceptSelf(vector<int>& nums) {
        int n = nums.size();
        vector<int> ans(n, 1);
        for (int i = 1; i < n; i++) ans[i] = ans[i - 1] * nums[i - 1];

        int suffix = 1;
        for (int i = n - 1; i >= 0; i--) {
            ans[i] *= suffix;
            suffix *= nums[i];
        }
        return ans;
    }
};
```
