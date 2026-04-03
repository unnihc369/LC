# Two Sum

## Navigate

Jump to a solution or section (click to scroll):

| | |
|--|--|
| **Overview** | [Description](#description) · [Prerequisites](#prerequisites) · [Common Pitfalls](#common-pitfalls) |
| **1. Brute Force** | [Section](#1-brute-force) — [Java](#1-brute-force-java) · [C++](#1-brute-force-cpp) · [Python](#1-brute-force-python) |
| **2. Sorting** | [Section](#2-sorting) — [Java](#2-sorting-java) · [C++](#2-sorting-cpp) · [Python](#2-sorting-python) |
| **3. Hash Map (Two Pass)** | [Section](#3-hash-map-two-pass) — [Java](#3-hash-map-two-pass-java) · [C++](#3-hash-map-two-pass-cpp) · [Python](#3-hash-map-two-pass-python) |
| **4. Hash Map (One Pass)** | [Section](#4-hash-map-one-pass) — [Java](#4-hash-map-one-pass-java) · [C++](#4-hash-map-one-pass-cpp) · [Python](#4-hash-map-one-pass-python) |

**Tip:** Each code block is inside a collapsible panel—click the language row to show or hide the source.

---

## Description

Given an integer array `nums` and an integer `target`, return **indices** `i` and `j` with `i != j` such that `nums[i] + nums[j] == target`.

You may assume **exactly one** valid answer exists (classic LeetCode wording). Return the two indices in any order unless the problem statement specifies otherwise.

**Constraints (typical):** `2 <= nums.length <= 10^4`, values and `target` fit in 32-bit signed integers; exactly one solution.

## Prerequisites

- **Arrays and nested loops:** brute force over pairs `(i, j)` with `i < j` or `i != j`.
- **Sorting:** `Arrays.sort` with comparator (Java), `sort` on pairs (C++), `sorted` (Python).
- **Two pointers:** on a **sorted** sequence, move `l` and `r` by comparing sum to `target`.
- **Hash map:** `HashMap` / `unordered_map` / `dict` for **value → index** (or complement lookup in one pass).
- **Big-O:** `O(n²)` brute force, `O(n log n)` sort-based, `O(n)` average with hashing.

---

## 1. Brute Force

### Intuition

Try **every unordered pair** of distinct indices. The first pair that sums to `target` is the answer. No extra space, but the time cost is quadratic.

### Algorithm

1. For each `i` from `0` to `n - 2`.
2. For each `j` from `i + 1` to `n - 1`, if `nums[i] + nums[j] == target`, return `[i, j]`.
3. If the problem guarantees a solution, you should not reach the end; otherwise return a sentinel (not needed on LeetCode with the usual assumption).

### Time & Space Complexity

| | Time | Space |
|---|------|--------|
| **Brute force** | **O(n²)** | **O(1)** |

<a id="1-brute-force-java"></a>
<details>
<summary><strong>Java</strong> — TC: O(n²), SC: O(1) · <em>click to show / hide code</em></summary>

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        for (int i = 0; i < nums.length; i++) {
            for (int j = i + 1; j < nums.length; j++) {
                if (nums[i] + nums[j] == target) {
                    return new int[] { i, j };
                }
            }
        }
        return new int[] { -1, -1 };
    }
}
```

</details>

<a id="1-brute-force-cpp"></a>
<details>
<summary><strong>C++</strong> — TC: O(n²), SC: O(1) · <em>click to show / hide code</em></summary>

```cpp
#include <vector>
using std::vector;

class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        int n = (int)nums.size();
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                if (nums[i] + nums[j] == target) return { i, j };
            }
        }
        return { -1, -1 };
    }
};
```

</details>

<a id="1-brute-force-python"></a>
<details>
<summary><strong>Python</strong> — TC: O(n²), SC: O(1) · <em>click to show / hide code</em></summary>

```python
class Solution:
    def twoSum(self, nums: list[int], target: int) -> list[int]:
        n = len(nums)
        for i in range(n):
            for j in range(i + 1, n):
                if nums[i] + nums[j] == target:
                    return [i, j]
        return [-1, -1]
```

</details>

---

## 2. Sorting

### Intuition

Sort **values paired with original indices**. Then use **two pointers** at the ends of the sorted list: if the sum is too small, move the left pointer right; if too large, move the right pointer left. When the sum matches `target`, return the **stored indices**, not the sorted positions.

You must keep **index** alongside each value so the answer refers to the original array.

### Algorithm

1. Build an array (or list) of pairs `(nums[i], i)` for each index `i`.
2. Sort by `nums[i]` (value).
3. Set `l = 0`, `r = n - 1`.
4. While `l < r`: let `sum = value[l] + value[r]`. If `sum == target`, return `[index[l], index[r]]` (order by index if required). If `sum < target`, `l++`; else `r--`.

### Time & Space Complexity

| | Time | Space |
|---|------|--------|
| **Sort + two pointers** | **O(n log n)** | **O(n)** for pairs |

<a id="2-sorting-java"></a>
<details>
<summary><strong>Java</strong> — TC: O(n log n), SC: O(n) · <em>click to show / hide code</em></summary>

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
            if (sum == target) return new int[] { arr[l][1], arr[r][1] };
            if (sum < target) l++;
            else r--;
        }
        return new int[] { -1, -1 };
    }
}
```

</details>

<a id="2-sorting-cpp"></a>
<details>
<summary><strong>C++</strong> — TC: O(n log n), SC: O(n) · <em>click to show / hide code</em></summary>

```cpp
#include <algorithm>
#include <utility>
#include <vector>
using std::pair;
using std::vector;

class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        vector<pair<int, int>> arr;
        for (int i = 0; i < (int)nums.size(); i++) arr.push_back({ nums[i], i });
        std::sort(arr.begin(), arr.end());
        int l = 0, r = (int)arr.size() - 1;
        while (l < r) {
            int sum = arr[l].first + arr[r].first;
            if (sum == target) return { arr[l].second, arr[r].second };
            if (sum < target) l++;
            else r--;
        }
        return { -1, -1 };
    }
};
```

</details>

<a id="2-sorting-python"></a>
<details>
<summary><strong>Python</strong> — TC: O(n log n), SC: O(n) · <em>click to show / hide code</em></summary>

```python
class Solution:
    def twoSum(self, nums: list[int], target: int) -> list[int]:
        arr = sorted([(nums[i], i) for i in range(len(nums))])
        l, r = 0, len(arr) - 1
        while l < r:
            s = arr[l][0] + arr[r][0]
            if s == target:
                return [arr[l][1], arr[r][1]]
            if s < target:
                l += 1
            else:
                r -= 1
        return [-1, -1]
```

</details>

---

## 3. Hash Map (Two Pass)

### Intuition

Store **value → index** in a hash map so each complement `target - nums[i]` can be looked up in **O(1)** average time. In the **first pass**, fill the map (later occurrences of the same value overwrite earlier ones). In the **second pass**, for each index `i`, check whether `need = target - nums[i]` exists in the map **and** that its index is **not** `i` (so you do not pair an element with itself).

### Algorithm

1. **Pass 1:** For each index `i`, set `map[nums[i]] = i`.
2. **Pass 2:** For each index `i`, set `need = target - nums[i]`. If `need` is in the map and `map[need] != i`, return the two indices (e.g. `i` and `map[need]`, ordered if needed).

### Time & Space Complexity

| | Time | Space |
|---|------|--------|
| **Hash map (two pass)** | **O(n)** average | **O(n)** |

<a id="3-hash-map-two-pass-java"></a>
<details>
<summary><strong>Java</strong> — TC: O(n) avg, SC: O(n) · <em>click to show / hide code</em></summary>

```java
import java.util.HashMap;
import java.util.Map;

class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++) map.put(nums[i], i);
        for (int i = 0; i < nums.length; i++) {
            int need = target - nums[i];
            if (map.containsKey(need) && map.get(need) != i) {
                return new int[] { i, map.get(need) };
            }
        }
        return new int[] { -1, -1 };
    }
}
```

</details>

<a id="3-hash-map-two-pass-cpp"></a>
<details>
<summary><strong>C++</strong> — TC: O(n) avg, SC: O(n) · <em>click to show / hide code</em></summary>

```cpp
#include <unordered_map>
#include <vector>
using std::unordered_map;
using std::vector;

class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> mp;
        for (int i = 0; i < (int)nums.size(); i++) mp[nums[i]] = i;
        for (int i = 0; i < (int)nums.size(); i++) {
            int need = target - nums[i];
            if (mp.count(need) && mp[need] != i) return { i, mp[need] };
        }
        return { -1, -1 };
    }
};
```

</details>

<a id="3-hash-map-two-pass-python"></a>
<details>
<summary><strong>Python</strong> — TC: O(n) avg, SC: O(n) · <em>click to show / hide code</em></summary>

```python
class Solution:
    def twoSum(self, nums: list[int], target: int) -> list[int]:
        index = {nums[i]: i for i in range(len(nums))}
        for i in range(len(nums)):
            need = target - nums[i]
            if need in index and index[need] != i:
                return [i, index[need]]
        return [-1, -1]
```

</details>

---

## 4. Hash Map (One Pass)

### Intuition

While scanning left to right, for each `nums[i]` the **complement** is `need = target - nums[i]`. If `need` was already seen, its index is in the map—return that index and `i`. Otherwise store `nums[i] -> i` and continue. You only need **one** pass because each pair is found when you reach the **second** index.

### Algorithm

1. Create an empty map `seen` from value to index.
2. For each index `i`:
   - `need = target - nums[i]`.
   - If `need` is in `seen`, return `[seen.get(need), i]`.
   - Else `seen[nums[i]] = i`.

### Time & Space Complexity

| | Time | Space |
|---|------|--------|
| **Hash map (one pass)** | **O(n)** average | **O(n)** |

<a id="4-hash-map-one-pass-java"></a>
<details>
<summary><strong>Java</strong> — TC: O(n) avg, SC: O(n) · <em>click to show / hide code</em></summary>

```java
import java.util.HashMap;
import java.util.Map;

class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> mp = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            int need = target - nums[i];
            if (mp.containsKey(need)) return new int[] { mp.get(need), i };
            mp.put(nums[i], i);
        }
        return new int[] { -1, -1 };
    }
}
```

</details>

<a id="4-hash-map-one-pass-cpp"></a>
<details>
<summary><strong>C++</strong> — TC: O(n) avg, SC: O(n) · <em>click to show / hide code</em></summary>

```cpp
#include <unordered_map>
#include <vector>
using std::unordered_map;
using std::vector;

class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> mp;
        for (int i = 0; i < (int)nums.size(); i++) {
            int need = target - nums[i];
            if (mp.count(need)) return { mp[need], i };
            mp[nums[i]] = i;
        }
        return { -1, -1 };
    }
};
```

</details>

<a id="4-hash-map-one-pass-python"></a>
<details>
<summary><strong>Python</strong> — TC: O(n) avg, SC: O(n) · <em>click to show / hide code</em></summary>

```python
class Solution:
    def twoSum(self, nums: list[int], target: int) -> list[int]:
        seen: dict[int, int] = {}
        for i, x in enumerate(nums):
            need = target - x
            if need in seen:
                return [seen[need], i]
            seen[x] = i
        return [-1, -1]
```

</details>

---

## Common Pitfalls

### Using the Same Element Twice

- **One pass:** Add `nums[i]` to the map **after** checking for `need`, so the same index is not reused.
- **Two pass:** Require `map[need] != i` when complement equals the current value at the same index (e.g. `target = 4`, `nums = [2, ...]` at a single `2`).

### Returning Values Instead of Indices

- The problem asks for **indices**, not the numbers themselves. Sorting-based solutions must return the **original** indices stored with each value.

### Handling Duplicate Values

- **Map from value → index:** duplicate values **overwrite**; the one-pass approach still works because you stop at the first valid pair.
- **Two pass:** The second pass uses `map[need] != i` so two different positions with the same value (e.g. `[3, 3]` and `target == 6`) still yield two distinct indices.

### Wrong Complement Calculation

- Use **`target - nums[i]`**, not `target + nums[i]` or `nums[i] - target`, unless you algebraically rearrange to the same thing.
- **Integer overflow** is rare in interview settings but in theory `target - nums[i]` can overflow for extreme bounds; know your language’s rules (e.g. use `long` if needed).
