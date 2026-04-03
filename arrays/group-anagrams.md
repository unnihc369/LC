# Group Anagrams

## Navigate

Jump to a solution or section (click to scroll):

| | |
|--|--|
| **Overview** | [Description](#description) · [Prerequisites](#prerequisites) · [Common Pitfalls](#common-pitfalls) |
| **1. Sorting** | [Section](#1-sorting) — [Java](#1-sorting-java) · [C++](#1-sorting-cpp) · [Python](#1-sorting-python) |
| **2. Hash Table** | [Section](#2-hash-table) — [Java](#2-hash-table-java) · [C++](#2-hash-table-cpp) · [Python](#2-hash-table-python) |

**Tip:** Each code block is inside a collapsible panel—click the language row to show or hide the source.

---

## Description

Given an array of strings `strs`, **group** the anagrams together. Anagrams are strings formed by rearranging the letters of another, using all characters exactly once.

You can return the groups in **any order**, and the order of strings inside each group is arbitrary unless the problem says otherwise.

**Constraints (typical LeetCode 49):** `1 <= strs.length <= 10^4`, `0 <= strs[i].length <= 100`; `strs[i]` consists of **lowercase English letters** only.

## Prerequisites

- **Anagrams:** same **multiset of characters** → same sorted string, or same **per-letter counts**.
- **Sorting:** sort characters of each word to obtain a **canonical key**—`O(k log k)` per word of length `k`.
- **Hash table:** `Map` / `unordered_map` / `dict` from **key → list of original strings**.
- **Big-O:** `n` strings, each length up to `k`—compare `O(n · k log k)` (sort keys) vs `O(n · k)` (count keys).

---

## 1. Sorting

### Intuition

Two words are anagrams iff sorting their characters yields the **same string**. Use that sorted string as a **group key**: all originals with the same key belong in one bucket.

### Algorithm

1. Create a map from **key string** to **list of strings** from the input.
2. For each `s` in `strs`, copy characters to an array, **sort** ascending, build `key = new String(sorted)`.
3. Append `s` to `map[key]`.
4. Return all values of the map as a list of groups.

### Time & Space Complexity

| | Time | Space |
|---|------|--------|
| **Sorting key** | **O(n · k log k)** * | **O(n · k)** |

\* `n = len(strs)`, `k` = max string length; sorting each word dominates.

<a id="1-sorting-java"></a>
<details>
<summary><strong>Java</strong> — TC: O(n·k log k), SC: O(n·k) · <em>click to show / hide code</em></summary>

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

</details>

<a id="1-sorting-cpp"></a>
<details>
<summary><strong>C++</strong> — TC: O(n·k log k), SC: O(n·k) · <em>click to show / hide code</em></summary>

```cpp
#include <algorithm>
#include <string>
#include <unordered_map>
#include <vector>
using std::string;
using std::unordered_map;
using std::vector;

class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        unordered_map<string, vector<string>> mp;
        for (string& s : strs) {
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

</details>

<a id="1-sorting-python"></a>
<details>
<summary><strong>Python</strong> — TC: O(n·k log k), SC: O(n·k) · <em>click to show / hide code</em></summary>

```python
from collections import defaultdict

class Solution:
    def groupAnagrams(self, strs: list[str]) -> list[list[str]]:
        mp: defaultdict[str, list[str]] = defaultdict(list)
        for s in strs:
            key = "".join(sorted(s))
            mp[key].append(s)
        return list(mp.values())
```

</details>

---

## 2. Hash Table

### Intuition

Instead of sorting, compute a **fixed-size frequency signature** over the alphabet (e.g. 26 counts for `a`–`z`). Anagrams share the same count vector. Encode that vector as a **string key** (with **unambiguous separators** so different count sequences never collide) or use a **hashable tuple** of counts. Group originals in a map keyed by that signature—still a **hash table**, with **O(k)** work per string and no `log k` sort factor.

### Algorithm

1. For each string `s`, allocate `count[26]` (or length of alphabet), increment `count[c - 'a']` for each character.
2. Build a key from the 26 counts—e.g. `"#c0#c1#..."` **with delimiters**, or a `tuple` of 26 integers in Python.
3. Map key → list of strings; append `s`.
4. Return all groups.

### Time & Space Complexity

| | Time | Space |
|---|------|--------|
| **Count signature** | **O(n · k)** | **O(n · k)** |

<a id="2-hash-table-java"></a>
<details>
<summary><strong>Java</strong> — TC: O(n·k), SC: O(n·k) · <em>click to show / hide code</em></summary>

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

</details>

<a id="2-hash-table-cpp"></a>
<details>
<summary><strong>C++</strong> — TC: O(n·k), SC: O(n·k) · <em>click to show / hide code</em></summary>

```cpp
#include <string>
#include <unordered_map>
#include <vector>
using std::string;
using std::unordered_map;
using std::vector;

class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        unordered_map<string, vector<string>> mp;
        for (string& s : strs) {
            vector<int> cnt(26, 0);
            for (char c : s) cnt[c - 'a']++;
            string key;
            for (int x : cnt) {
                key.push_back('#');
                key += std::to_string(x);
            }
            mp[key].push_back(s);
        }
        vector<vector<string>> ans;
        for (auto& it : mp) ans.push_back(it.second);
        return ans;
    }
};
```

</details>

<a id="2-hash-table-python"></a>
<details>
<summary><strong>Python</strong> — TC: O(n·k), SC: O(n·k) · <em>click to show / hide code</em></summary>

```python
from collections import defaultdict

class Solution:
    def groupAnagrams(self, strs: list[str]) -> list[list[str]]:
        mp: defaultdict[tuple[int, ...], list[str]] = defaultdict(list)
        for s in strs:
            cnt = [0] * 26
            for c in s:
                cnt[ord(c) - ord("a")] += 1
            key = tuple(cnt)
            mp[key].append(s)
        return list(mp.values())
```

</details>

---

## Common Pitfalls

### Using a Mutable Key Type for the Hash Map

- **Java:** `char[]` is **not** a valid `Map` key (reference equality, mutable). Always use **`String`** (or copy to `String` after sorting).
- **Python:** **`list`** is not hashable—use **`tuple`** for count vectors, or a **string** key.
- **C++:** `vector<int>` as `unordered_map` key needs **custom hash**; using a **`string`** signature avoids that.

### Assuming Input Contains Only Lowercase Letters

- Indexing with **`c - 'a'`** or **`freq[26]`** assumes **`a`–`z`** only. Uppercase, spaces, digits, or Unicode require a **different alphabet size**, **`Character` keys** in a map, or **Unicode-aware** normalization—do not hard-code 26 without checking constraints.

### Creating a New Key Format That Has Collisions

- **Ambiguous concatenation:** Building a key by **jamming counts without separators** (e.g. counts `1` and `2` vs `12`) can merge **different** frequency profiles into one key. Use **delimiters** (`"#" + count` per slot), **fixed-width** encoding, or a **tuple** of counts.
- **Sorted-string keys** avoid this class of bug: the sorted form is a true canonical form for anagrams over the same alphabet.
