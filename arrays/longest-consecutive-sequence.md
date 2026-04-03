# Longest Consecutive Sequence

## Navigate

Jump to a solution or section (click to scroll):

| | |
|--|--|
| **Overview** | [Description](#description) · [Prerequisites](#prerequisites) · [Common Pitfalls](#common-pitfalls) |
| **1. Brute Force** | [Section](#1-brute-force) — [Java](#1-brute-force-java) · [C++](#1-brute-force-cpp) · [Python](#1-brute-force-python) |
| **2. Sorting** | [Section](#2-sorting) — [Java](#2-sorting-java) · [C++](#2-sorting-cpp) · [Python](#2-sorting-python) |
| **3. Hash Set** | [Section](#3-hash-set) — [Java](#3-hash-set-java) · [C++](#3-hash-set-cpp) · [Python](#3-hash-set-python) |
| **4. Hash Map** | [Section](#4-hash-map) — [Java](#4-hash-map-java) · [C++](#4-hash-map-cpp) · [Python](#4-hash-map-python) |

**Tip:** Each code block is inside a collapsible panel—click the language row to show or hide the source.

---

## Description

Given an **unsorted** array of integers `nums`, return the **length** of the longest run of **consecutive** integers. Consecutive means values differ by **1** (e.g. `100, 101, 102`), not necessarily adjacent in the array.

**Constraints (typical LeetCode 128):** `0 <= nums.length <= 10^5`, values in roughly `−10^9` to `10^9`.

## Prerequisites

- **Consecutive values:** a streak is defined by **value** equality to `previous + 1`, not index order.
- **Sets / maps:** `O(1)` average lookup for “does `x` exist?”
- **Sorting:** `O(n log n)` to align values, then one scan.
- **Big-O:** aim for **O(n)** with hashing vs **O(n log n)** sort vs **O(n²)** or worse naive search.

---

## 1. Brute Force

### Intuition

For each element `x`, treat it as a potential start and walk `x+1, x+2, …` while each next value **appears somewhere** in `nums`, using a **linear scan** to test membership. Repeat for every element and take the maximum length.

### Algorithm

1. Define `exists(nums, target)` by scanning the array.
2. For each `x` in `nums`, set `len = 1`, `cur = x`.
3. While `exists(nums, cur + 1)`, increment `cur` and `len`.
4. Track the maximum `len` over all `x`.

### Time & Space Complexity

| | Time | Space |
|---|------|--------|
| **Brute force** | **O(n³)** * | **O(1)** |

\* Up to `n` starting points, up to `n` steps each, each `exists` is **O(n)**.

<a id="1-brute-force-java"></a>
<details>
<summary><strong>Java</strong> — TC: O(n³), SC: O(1) · <em>click to show / hide code</em></summary>

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

</details>

<a id="1-brute-force-cpp"></a>
<details>
<summary><strong>C++</strong> — TC: O(n³), SC: O(1) · <em>click to show / hide code</em></summary>

```cpp
#include <algorithm>
#include <vector>
using std::max;
using std::vector;

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

</details>

<a id="1-brute-force-python"></a>
<details>
<summary><strong>Python</strong> — TC: O(n³), SC: O(1) · <em>click to show / hide code</em></summary>

```python
class Solution:
    def longestConsecutive(self, nums: list[int]) -> int:
        def exists(target: int) -> bool:
            return target in nums  # O(n) per check on list

        best = 0
        for x in nums:
            cur, length = x, 1
            while exists(cur + 1):
                cur += 1
                length += 1
            best = max(best, length)
        return best
```

</details>

---

## 2. Sorting

### Intuition

Sort the values. A longest consecutive run becomes a **longest contiguous segment** in the sorted order where each value is **previous + 1**. Skip **duplicate** values without breaking the streak length incorrectly.

### Algorithm

1. If `nums` is empty, return `0`.
2. Sort `nums` in non-decreasing order.
3. Scan with streak `cur`; if `nums[i] == nums[i-1]` continue; if `nums[i] == nums[i-1] + 1` increment streak; else reset streak to `1`.
4. Return the maximum streak.

### Time & Space Complexity

| | Time | Space |
|---|------|--------|
| **Sorting** | **O(n log n)** | **O(1)** extra * |

\* In-place sort; implementation may use `O(log n)` stack.

<a id="2-sorting-java"></a>
<details>
<summary><strong>Java</strong> — TC: O(n log n), SC: O(1) aux · <em>click to show / hide code</em></summary>

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

</details>

<a id="2-sorting-cpp"></a>
<details>
<summary><strong>C++</strong> — TC: O(n log n), SC: O(1) aux · <em>click to show / hide code</em></summary>

```cpp
#include <algorithm>
#include <vector>
using std::max;
using std::vector;

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

</details>

<a id="2-sorting-python"></a>
<details>
<summary><strong>Python</strong> — TC: O(n log n), SC: O(1) aux * · <em>click to show / hide code</em></summary>

```python
class Solution:
    def longestConsecutive(self, nums: list[int]) -> int:
        if not nums:
            return 0
        nums.sort()
        best = cur = 1
        for i in range(1, len(nums)):
            if nums[i] == nums[i - 1]:
                continue
            if nums[i] == nums[i - 1] + 1:
                cur += 1
            else:
                cur = 1
            best = max(best, cur)
        return best
```

*Timsort may use extra memory.*

</details>

---

## 3. Hash Set

### Intuition

Put all values in a **set** for **O(1)** lookups. A consecutive sequence has a unique **minimum** value: `num` such that `num - 1` is **not** in the set. Only **start** counting from those minima, then walk `num+1, num+2, …` in the set. Each value belongs to at most one inner walk, so total work is **O(n)**.

### Algorithm

1. Insert every `nums[i]` into a hash set.
2. For each `x` in the set, if `x - 1` is **not** in the set, start a run from `x`, extending while `x+1` exists, counting length.
3. Return the maximum length (use `0` if `nums` is empty).

### Time & Space Complexity

| | Time | Space |
|---|------|--------|
| **Hash set (sequence starts)** | **O(n)** average | **O(n)** |

<a id="3-hash-set-java"></a>
<details>
<summary><strong>Java</strong> — TC: O(n) avg, SC: O(n) · <em>click to show / hide code</em></summary>

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

</details>

<a id="3-hash-set-cpp"></a>
<details>
<summary><strong>C++</strong> — TC: O(n) avg, SC: O(n) · <em>click to show / hide code</em></summary>

```cpp
#include <algorithm>
#include <unordered_set>
#include <vector>
using std::max;
using std::unordered_set;
using std::vector;

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

</details>

<a id="3-hash-set-python"></a>
<details>
<summary><strong>Python</strong> — TC: O(n) avg, SC: O(n) · <em>click to show / hide code</em></summary>

```python
class Solution:
    def longestConsecutive(self, nums: list[int]) -> int:
        st = set(nums)
        best = 0
        for x in st:
            if x - 1 not in st:
                cur, length = x, 1
                while cur + 1 in st:
                    cur += 1
                    length += 1
                best = max(best, length)
        return best
```

</details>

---

## 4. Hash Map

### Intuition

Process each number once. Maintain a map from some keys to the **current length** of a consecutive interval after **merging** neighbors. When inserting `num`, look up lengths stored at `num - 1` and `num + 1` (defaults `0`), merge into `left + right + 1`, and **write that length back** at the two **boundary** positions `num - left` and `num + right` so future inserts can merge correctly. **Skip** duplicates with `if (map already has num)`.

### Algorithm

1. Initialize empty map `lengths` (value → streak length for boundary bookkeeping) and `best = 0`.
2. For each `num` in `nums`:
   - If `num` is already a key, **continue** (duplicate).
   - Let `lenLeft = lengths.getOrDefault(num - 1, 0)`, `lenRight = lengths.getOrDefault(num + 1, 0)`.
   - `sum = lenLeft + lenRight + 1`.
   - Put `sum` at `num`, at `num - lenLeft`, and at `num + lenRight`.
   - `best = max(best, sum)`.
3. Return `best`.

### Time & Space Complexity

| | Time | Space |
|---|------|--------|
| **Hash map (boundary merge)** | **O(n)** average | **O(n)** |

<a id="4-hash-map-java"></a>
<details>
<summary><strong>Java</strong> — TC: O(n) avg, SC: O(n) · <em>click to show / hide code</em></summary>

```java
import java.util.HashMap;
import java.util.Map;

class Solution {
    public int longestConsecutive(int[] nums) {
        Map<Integer, Integer> mp = new HashMap<>();
        int best = 0;
        for (int num : nums) {
            if (mp.containsKey(num)) continue;
            int left = mp.getOrDefault(num - 1, 0);
            int right = mp.getOrDefault(num + 1, 0);
            int sum = left + right + 1;
            mp.put(num, sum);
            mp.put(num - left, sum);
            mp.put(num + right, sum);
            best = Math.max(best, sum);
        }
        return best;
    }
}
```

</details>

<a id="4-hash-map-cpp"></a>
<details>
<summary><strong>C++</strong> — TC: O(n) avg, SC: O(n) · <em>click to show / hide code</em></summary>

```cpp
#include <algorithm>
#include <unordered_map>
#include <vector>
using std::max;
using std::unordered_map;
using std::vector;

class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        unordered_map<int, int> mp;
        int best = 0;
        for (int num : nums) {
            if (mp.count(num)) continue;
            int left = mp.count(num - 1) ? mp[num - 1] : 0;
            int right = mp.count(num + 1) ? mp[num + 1] : 0;
            int sum = left + right + 1;
            mp[num] = sum;
            mp[num - left] = sum;
            mp[num + right] = sum;
            best = max(best, sum);
        }
        return best;
    }
};
```

</details>

<a id="4-hash-map-python"></a>
<details>
<summary><strong>Python</strong> — TC: O(n) avg, SC: O(n) · <em>click to show / hide code</em></summary>

```python
class Solution:
    def longestConsecutive(self, nums: list[int]) -> int:
        mp: dict[int, int] = {}
        best = 0
        for num in nums:
            if num in mp:
                continue
            left = mp.get(num - 1, 0)
            right = mp.get(num + 1, 0)
            total = left + right + 1
            mp[num] = total
            mp[num - left] = total
            mp[num + right] = total
            best = max(best, total)
        return best
```

</details>

---

## Common Pitfalls

### Starting a Sequence from Every Number

- If you extend `num+1, num+2, …` for **every** `num` without checking that `num-1` is absent, you redo the same chain many times (**O(n²)** or worse). **Hash set:** only start when `x - 1 ∉ set`. **Sorting:** one linear scan after sort suffices.

### Not Handling Duplicates Properly

- **Sorting:** when `nums[i] == nums[i-1]`, **skip** without resetting the streak (duplicates are not consecutive values).
- **Hash map merge:** skip if **`num` was already processed** (`containsKey` / `count`), otherwise the same value extends the interval multiple times and corrupts lengths.

### Forgetting to Handle Empty Input

- If `nums.length == 0`, the longest streak length is **`0`**. Sorting-based code should early-return; set/map loops naturally yield `0` for `best` but verify edge cases when initializing streak to `1`.
