# Contains Duplicate

## Navigate

Jump to a solution or section (click to scroll):

| | |
|--|--|
| **Overview** | [Description](#description) · [Prerequisites](#prerequisites) · [Common Pitfalls](#common-pitfalls) |
| **1. Brute Force** | [Section](#1-brute-force) — [Java](#1-brute-force-java) · [C++](#1-brute-force-cpp) · [Python](#1-brute-force-python) |
| **2. Sorting** | [Section](#2-sorting) — [Java](#2-sorting-java) · [C++](#2-sorting-cpp) · [Python](#2-sorting-python) |
| **3. Hash Set** | [Section](#3-hash-set) — [Java](#3-hash-set-java) · [C++](#3-hash-set-cpp) · [Python](#3-hash-set-python) |
| **4. Hash Set Length** | [Section](#4-hash-set-length) — [Java](#4-hash-set-length-java) · [C++](#4-hash-set-length-cpp) · [Python](#4-hash-set-length-python) |

**Tip:** Each code block is inside a collapsible panel—click the language row to show or hide the source.

---

## Description

Given an integer array `nums`, return `true` if any value appears **at least twice**, and `false` if every element is distinct.

**Constraints (typical):** `1 <= nums.length <= 10^5`, values fit in 32-bit signed integers.

## Prerequisites

- **Arrays and loops:** nested iteration for brute force.
- **Sorting:** `Arrays.sort` (Java), `sort` (C++), `sorted` or in-place sort (Python).
- **Hashing:** `HashSet` / `unordered_set` / `set` for uniqueness checks in average `O(1)`.
- **Big-O:** know trade-offs—`O(n²)` vs `O(n log n)` vs `O(n)`.

---

## 1. Brute Force

### Intuition

If any two different indices hold the same value, there is a duplicate. We can check every pair `(i, j)` with `i < j` without extra memory.

### Algorithm

1. For each index `i` from `0` to `n - 2`.
2. For each `j` from `i + 1` to `n - 1`, if `nums[i] == nums[j]`, return `true`.
3. If no pair matches, return `false`.

### Time & Space Complexity

| | Time | Space |
|---|------|--------|
| **Brute force** | **O(n²)** | **O(1)** |

<a id="1-brute-force-java"></a>
<details>
<summary><strong>Java</strong> — TC: O(n²), SC: O(1) · <em>click to show / hide code</em></summary>

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

</details>

<a id="1-brute-force-cpp"></a>
<details>
<summary><strong>C++</strong> — TC: O(n²), SC: O(1) · <em>click to show / hide code</em></summary>

```cpp
#include <vector>
using std::vector;

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

</details>

<a id="1-brute-force-python"></a>
<details>
<summary><strong>Python</strong> — TC: O(n²), SC: O(1) · <em>click to show / hide code</em></summary>

```python
class Solution:
    def containsDuplicate(self, nums: list[int]) -> bool:
        n = len(nums)
        for i in range(n):
            for j in range(i + 1, n):
                if nums[i] == nums[j]:
                    return True
        return False
```

</details>

---

## 2. Sorting

### Intuition

After sorting, equal values become **adjacent**. A single linear scan can detect any `nums[i] == nums[i - 1]`.

### Algorithm

1. Sort `nums` in non-decreasing order (in-place).
2. Walk from index `1` to `n - 1`; if `nums[i] == nums[i - 1]`, return `true`.
3. Otherwise return `false`.

### Time & Space Complexity

| | Time | Space |
|---|------|--------|
| **Sorting** | **O(n log n)** | **O(1)** extra * (see note below) |

\* In Java/C++, typical sort may use `O(log n)` stack or internal buffer depending on implementation; still regarded as **O(1)** auxiliary relative to output for interviews unless specified.

<a id="2-sorting-java"></a>
<details>
<summary><strong>Java</strong> — TC: O(n log n), SC: O(1) aux · <em>click to show / hide code</em></summary>

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

</details>

<a id="2-sorting-cpp"></a>
<details>
<summary><strong>C++</strong> — TC: O(n log n), SC: O(1) aux · <em>click to show / hide code</em></summary>

```cpp
#include <vector>
#include <algorithm>
using std::vector;

class Solution {
public:
    bool containsDuplicate(vector<int>& nums) {
        std::sort(nums.begin(), nums.end());
        for (int i = 1; i < (int)nums.size(); i++) {
            if (nums[i] == nums[i - 1]) return true;
        }
        return false;
    }
};
```

</details>

<a id="2-sorting-python"></a>
<details>
<summary><strong>Python</strong> — TC: O(n log n), SC: O(1)* · <em>click to show / hide code</em></summary>

```python
class Solution:
    def containsDuplicate(self, nums: list[int]) -> bool:
        nums.sort()
        for i in range(1, len(nums)):
            if nums[i] == nums[i - 1]:
                return True
        return False
```

*Timsort may use O(n) internal space in worst case.*

</details>

---

## 3. Hash Set

### Intuition

A **set** stores each value at most once. While scanning the array, if we try to add a value that is already in the set, we have seen it before—duplicate.

### Algorithm

1. Create an empty hash set `seen`.
2. For each `x` in `nums`:
   - If `x` is already in `seen`, return `true`.
   - Else insert `x` into `seen`.
3. Return `false`.

### Time & Space Complexity

| | Time | Space |
|---|------|--------|
| **Hash set** | **O(n)** average | **O(n)** |

Worst-case hash behavior can degrade; still standard for this problem.

<a id="3-hash-set-java"></a>
<details>
<summary><strong>Java</strong> — TC: O(n) avg, SC: O(n) · <em>click to show / hide code</em></summary>

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

</details>

<a id="3-hash-set-cpp"></a>
<details>
<summary><strong>C++</strong> — TC: O(n) avg, SC: O(n) · <em>click to show / hide code</em></summary>

```cpp
#include <vector>
#include <unordered_set>
using std::vector;

class Solution {
public:
    bool containsDuplicate(vector<int>& nums) {
        std::unordered_set<int> seen;
        for (int x : nums) {
            if (seen.count(x)) return true;
            seen.insert(x);
        }
        return false;
    }
};
```

</details>

<a id="3-hash-set-python"></a>
<details>
<summary><strong>Python</strong> — TC: O(n) avg, SC: O(n) · <em>click to show / hide code</em></summary>

```python
class Solution:
    def containsDuplicate(self, nums: list[int]) -> bool:
        seen = set()
        for x in nums:
            if x in seen:
                return True
            seen.add(x)
        return False
```

</details>

---

## 4. Hash Set Length

### Intuition

If there are **no duplicates**, the number of **distinct** values equals `n`. Putting every element into a set shrinks the count only when duplicates exist: `len(set) < len(nums)`.

### Algorithm

1. Insert all elements of `nums` into a set (or build set from iterable).
2. Return `len(set) != len(nums)` (or compare sizes however your language expresses it).

### Time & Space Complexity

| | Time | Space |
|---|------|--------|
| **Set length** | **O(n)** average | **O(n)** |

<a id="4-hash-set-length-java"></a>
<details>
<summary><strong>Java</strong> — TC: O(n) avg, SC: O(n) · <em>click to show / hide code</em></summary>

```java
import java.util.HashSet;
import java.util.Set;

class Solution {
    public boolean containsDuplicate(int[] nums) {
        Set<Integer> seen = new HashSet<>();
        for (int x : nums) seen.add(x);
        return seen.size() != nums.length;
    }
}
```

</details>

<a id="4-hash-set-length-cpp"></a>
<details>
<summary><strong>C++</strong> — TC: O(n) avg, SC: O(n) · <em>click to show / hide code</em></summary>

```cpp
#include <vector>
#include <unordered_set>
using std::vector;

class Solution {
public:
    bool containsDuplicate(vector<int>& nums) {
        std::unordered_set<int> seen(nums.begin(), nums.end());
        return seen.size() != nums.size();
    }
};
```

</details>

<a id="4-hash-set-length-python"></a>
<details>
<summary><strong>Python</strong> — TC: O(n) avg, SC: O(n) · <em>click to show / hide code</em></summary>

```python
class Solution:
    def containsDuplicate(self, nums: list[int]) -> bool:
        return len(set(nums)) != len(nums)
```

</details>

---

## Common Pitfalls

### Using Wrong Comparison in Brute Force

- **Same index:** Comparing `nums[i] == nums[j]` when `i == j` is always true for non-empty arrays and wrongly reports a “duplicate.” Always enforce **`j > i`** (or use two loops that never use the same index).
- **Early exit:** Returning `false` after only checking the first element against others—every pair must be considered (or use a better algorithm).

### Modifying Input Array Unexpectedly

- **Sorting mutates `nums`.** If the caller must keep the original order (e.g., shared state, multiple queries), **copy first** (`nums.copy()` in Python, `Arrays.copyOf` / new array in Java, `vector` copy in C++) then sort the copy—or use a hash set instead of sorting.
- **Document or avoid in-place sort** when the API contract implies read-only input.
