# Valid Anagram

## Navigate

Jump to a solution or section (click to scroll):

| | |
|--|--|
| **Overview** | [Description](#description) · [Prerequisites](#prerequisites) · [Common Pitfalls](#common-pitfalls) |
| **1. Sorting** | [Section](#1-sorting) — [Java](#1-sorting-java) · [C++](#1-sorting-cpp) · [Python](#1-sorting-python) |
| **2. Hash Map** | [Section](#2-hash-map) — [Java](#2-hash-map-java) · [C++](#2-hash-map-cpp) · [Python](#2-hash-map-python) |
| **3. Hash Table (Using Array)** | [Section](#3-hash-table-using-array) — [Java](#3-hash-table-using-array-java) · [C++](#3-hash-table-using-array-cpp) · [Python](#3-hash-table-using-array-python) |

**Tip:** Each code block is inside a collapsible panel—click the language row to show or hide the source.

---

## Description

Given two strings `s` and `t`, return `true` if `t` is an **anagram** of `s`—that is, `t` can be formed by **rearranging** the letters of `s` using all the original letters exactly once.

**Constraints (typical LeetCode 242):** `1 <= s.length, t.length <= 5 * 10^4`; strings consist of **lowercase English letters** `a`–`z` only.

## Prerequisites

- **Character frequency:** two strings are anagrams iff **multisets of characters** are equal.
- **Sorting:** sort characters (or code units) and compare—`O(n log n)` time.
- **Hash map / frequency table:** count each character in **O(n)** time; map for arbitrary alphabets, **fixed array** when the alphabet is small and known.
- **Big-O:** compare `O(n log n)` sort vs `O(n)` counting with `O(1)` or `O(σ)` extra space (`σ` = alphabet size).

---

## 1. Sorting

### Intuition

Anagrams have the **same characters with the same counts**. If you sort the characters of both strings, identical multisets become **identical strings**. Compare the sorted arrays (or sorted strings).

### Algorithm

1. If `len(s) != len(t)`, return `false`.
2. Convert both strings to char arrays (or sortable views), **sort** each in ascending order.
3. Return whether the two sorted sequences are **equal** element-wise.

### Time & Space Complexity

| | Time | Space |
|---|------|--------|
| **Sorting** | **O(n log n)** * | **O(n)** for char copies * |

\* `n = s.length`; sort may use `O(log n)` stack internally; space often stated as **O(1)** extra in interviews if sorting in place on copies is counted separately.

<a id="1-sorting-java"></a>
<details>
<summary><strong>Java</strong> — TC: O(n log n), SC: O(n) · <em>click to show / hide code</em></summary>

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

</details>

<a id="1-sorting-cpp"></a>
<details>
<summary><strong>C++</strong> — TC: O(n log n), SC: O(n) · <em>click to show / hide code</em></summary>

```cpp
#include <algorithm>
#include <string>
using std::string;

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

</details>

<a id="1-sorting-python"></a>
<details>
<summary><strong>Python</strong> — TC: O(n log n), SC: O(n) · <em>click to show / hide code</em></summary>

```python
class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        if len(s) != len(t):
            return False
        return sorted(s) == sorted(t)
```

</details>

---

## 2. Hash Map

### Intuition

Count how many times each character appears in `s`, then **subtract** using `t`. If every count ends at **zero**, the multisets match. A **hash map** stores **character → count** and works for **any** character set (Unicode, mixed case) without assuming a fixed index range—unlike a raw array of length 26.

### Algorithm

1. If lengths differ, return `false`.
2. Build a frequency map for `s`: for each character `c`, increment `count[c]`.
3. For each character `c` in `t`, decrement `count[c]`.
4. If any value is non-zero (or if a key in `t` was missing in `s`), return `false`; otherwise return `true`.

Alternatively: merge increments and decrements in one map, then verify all values are zero.

### Time & Space Complexity

| | Time | Space |
|---|------|--------|
| **Hash map** | **O(n)** average | **O(k)** * |

\* `k` = number of **distinct** characters across `s` and `t` (at most `n`); for lowercase English letters, `k <= 26`.

<a id="2-hash-map-java"></a>
<details>
<summary><strong>Java</strong> — TC: O(n) avg, SC: O(k) · <em>click to show / hide code</em></summary>

```java
import java.util.HashMap;
import java.util.Map;

class Solution {
    public boolean isAnagram(String s, String t) {
        if (s.length() != t.length()) return false;
        Map<Character, Integer> cnt = new HashMap<>();
        for (char c : s.toCharArray()) cnt.merge(c, 1, Integer::sum);
        for (char c : t.toCharArray()) cnt.merge(c, -1, Integer::sum);
        for (int v : cnt.values()) if (v != 0) return false;
        return true;
    }
}
```

</details>

<a id="2-hash-map-cpp"></a>
<details>
<summary><strong>C++</strong> — TC: O(n) avg, SC: O(k) · <em>click to show / hide code</em></summary>

```cpp
#include <string>
#include <unordered_map>
using std::string;
using std::unordered_map;

class Solution {
public:
    bool isAnagram(string s, string t) {
        if (s.size() != t.size()) return false;
        unordered_map<char, int> cnt;
        for (char c : s) cnt[c]++;
        for (char c : t) cnt[c]--;
        for (auto& p : cnt)
            if (p.second != 0) return false;
        return true;
    }
};
```

</details>

<a id="2-hash-map-python"></a>
<details>
<summary><strong>Python</strong> — TC: O(n) avg, SC: O(k) · <em>click to show / hide code</em></summary>

```python
from collections import defaultdict

class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        if len(s) != len(t):
            return False
        cnt: defaultdict[str, int] = defaultdict(int)
        for c in s:
            cnt[c] += 1
        for c in t:
            cnt[c] -= 1
        return all(v == 0 for v in cnt.values())
```

</details>

---

## 3. Hash Table (Using Array)

### Intuition

When the alphabet is **fixed and small** (here, 26 lowercase letters), use an **array of length 26** as a direct-address table: index `c - 'a'` holds the net count for that letter. No hashing overhead, cache-friendly, same **O(n)** time as a hash map for this problem.

### Algorithm

1. If `len(s) != len(t)`, return `false`.
2. Create `int[26]` (or `vector<int>(26)`) initialized to zero.
3. For each character in `s`, do `freq[c - 'a']++`.
4. For each character in `t`, do `freq[c - 'a']--`.
5. Return `true` iff **every** entry in `freq` is zero.

### Time & Space Complexity

| | Time | Space |
|---|------|--------|
| **Array counts** | **O(n)** | **O(1)** * |

\* **O(26) = O(1)** extra space for the fixed English lowercase alphabet.

<a id="3-hash-table-using-array-java"></a>
<details>
<summary><strong>Java</strong> — TC: O(n), SC: O(1) · <em>click to show / hide code</em></summary>

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

</details>

<a id="3-hash-table-using-array-cpp"></a>
<details>
<summary><strong>C++</strong> — TC: O(n), SC: O(1) · <em>click to show / hide code</em></summary>

```cpp
#include <string>
#include <vector>
using std::string;
using std::vector;

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

</details>

<a id="3-hash-table-using-array-python"></a>
<details>
<summary><strong>Python</strong> — TC: O(n), SC: O(1) · <em>click to show / hide code</em></summary>

```python
class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        if len(s) != len(t):
            return False
        freq = [0] * 26
        for c in s:
            freq[ord(c) - ord("a")] += 1
        for c in t:
            freq[ord(c) - ord("a")] -= 1
        return all(x == 0 for x in freq)
```

</details>

---

## Common Pitfalls

### Forgetting to Check Length First

- If `len(s) != len(t)`, the strings **cannot** be anagrams—return `false` **before** sorting or counting. Skipping this can let algorithms “pass” spuriously when one string is a **sub-multiset** of the other (e.g. `"a"` vs `"ab"`).

### Case Sensitivity Issues

- **`'A'` vs `'a'`** are different characters. If the problem expects **case-insensitive** comparison, normalize (e.g. `toLowerCase`) **consistently** before counting or sorting.
- The **array-of-26** trick assumes **lowercase `'a'`–`'z'`** only; inputs with spaces, digits, or Unicode need a **map** or a larger / different indexing scheme.
