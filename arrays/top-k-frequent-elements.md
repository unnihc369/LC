# Top K Frequent Elements

## Navigate

Jump to a solution or section (click to scroll):

| | |
|--|--|
| **Overview** | [Description](#description) · [Prerequisites](#prerequisites) · [Common Pitfalls](#common-pitfalls) |
| **1. Sorting** | [Section](#1-sorting) — [Java](#1-sorting-java) · [C++](#1-sorting-cpp) · [Python](#1-sorting-python) |
| **2. Min-Heap** | [Section](#2-min-heap) — [Java](#2-min-heap-java) · [C++](#2-min-heap-cpp) · [Python](#2-min-heap-python) |
| **3. Bucket Sort** | [Section](#3-bucket-sort) — [Java](#3-bucket-sort-java) · [C++](#3-bucket-sort-cpp) · [Python](#3-bucket-sort-python) |

**Tip:** Each code block is inside a collapsible panel—click the language row to show or hide the source.

---

## Description

Given an integer array `nums` and an integer `k`, return the **`k` most frequent elements**. You may return the answer **in any order**.

**Constraints (typical LeetCode 347):** `1 <= nums.length <= 10^5`, `-10^4 <= nums[i] <= 10^4`, `k` is in the range `[1, number of unique elements in the array]`, and it is **guaranteed** that the answer is **unique** (the set of `k` highest frequencies is unambiguous—ties in frequency still allow any valid choice of `k` elements unless stated otherwise).

## Prerequisites

- **Frequency counting:** `HashMap` / `unordered_map` / `Counter` for value → count in **O(n)**.
- **Sorting:** sort **unique values** (or `(value, count)` pairs) by count descending—`O(m log m)` for `m` distinct values.
- **Heaps:** **min-heap** of size `k` on **frequency** keeps the `k` largest counts with **O(log k)** pushes/pops.
- **Bucket sort (by frequency):** index buckets by **frequency** `1 .. n`; linear scan from high frequency down.

---

## 1. Sorting

### Intuition

Count occurrences of each number, then **sort** the distinct numbers (or their `(value, frequency)` pairs) by **frequency** in **descending** order. The first `k` entries are the `k` most frequent elements.

### Algorithm

1. Build a frequency map: one pass over `nums`.
2. Copy entries to a list (or sortable container).
3. **Sort** by frequency **descending** (larger count first).
4. Take the first `k` **values** (keys).

### Time & Space Complexity

| | Time | Space |
|---|------|--------|
| **Sort by frequency** | **O(n + m log m)** * | **O(m)** |

\* `n = nums.length`, `m` = number of distinct values (`m <= n`); counting is `O(n)`, sorting distinct entries is `O(m log m)`.

<a id="1-sorting-java"></a>
<details>
<summary><strong>Java</strong> — TC: O(n + m log m), SC: O(m) · <em>click to show / hide code</em></summary>

```java
import java.util.*;

class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> freq = new HashMap<>();
        for (int x : nums) freq.merge(x, 1, Integer::sum);

        List<Map.Entry<Integer, Integer>> list = new ArrayList<>(freq.entrySet());
        list.sort((a, b) -> b.getValue() - a.getValue());

        int[] ans = new int[k];
        for (int i = 0; i < k; i++) ans[i] = list.get(i).getKey();
        return ans;
    }
}
```

</details>

<a id="1-sorting-cpp"></a>
<details>
<summary><strong>C++</strong> — TC: O(n + m log m), SC: O(m) · <em>click to show / hide code</em></summary>

```cpp
#include <algorithm>
#include <unordered_map>
#include <vector>
using std::pair;
using std::unordered_map;
using std::vector;

class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        unordered_map<int, int> freq;
        for (int x : nums) freq[x]++;

        vector<pair<int, int>> arr(freq.begin(), freq.end());
        sort(arr.begin(), arr.end(), [](const pair<int, int>& a, const pair<int, int>& b) {
            return a.second > b.second;
        });

        vector<int> ans;
        for (int i = 0; i < k; i++) ans.push_back(arr[i].first);
        return ans;
    }
};
```

</details>

<a id="1-sorting-python"></a>
<details>
<summary><strong>Python</strong> — TC: O(n + m log m), SC: O(m) · <em>click to show / hide code</em></summary>

```python
from collections import Counter

class Solution:
    def topKFrequent(self, nums: list[int], k: int) -> list[int]:
        freq = Counter(nums)
        return [x for x, _ in sorted(freq.items(), key=lambda p: -p[1])[:k]]
```

</details>

---

## 2. Min-Heap

### Intuition

After counting frequencies, you only need the **`k` largest** counts among `m` candidates. Maintain a **min-heap** ordered by **frequency** (and break ties by value if needed). The heap holds at most `k` pairs `(value, frequency)`; if it grows beyond `k`, **evict** the **smallest** frequency—so the heap always stores the `k` highest frequencies seen so far.

Using a **max-heap of size `k`** on frequency is less natural here; the standard trick is **`k`-size min-heap** on frequency.

### Algorithm

1. Count frequencies for all distinct numbers.
2. Initialize an empty min-heap comparing by **frequency** (second component).
3. For each `(value, count)` in the map: push into the heap; if size **> `k`**, pop the minimum.
4. Extract the `k` values from the heap (order may vary).

### Time & Space Complexity

| | Time | Space |
|---|------|--------|
| **Min-heap (size k)** | **O(n + m log k)** * | **O(m + k)** |

\* `m` pushes each costing `O(log k)` after the heap stabilizes at size `k`.

<a id="2-min-heap-java"></a>
<details>
<summary><strong>Java</strong> — TC: O(n + m log k), SC: O(m + k) · <em>click to show / hide code</em></summary>

```java
import java.util.*;

class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> freq = new HashMap<>();
        for (int x : nums) freq.merge(x, 1, Integer::sum);

        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> a[1] - b[1]);
        for (Map.Entry<Integer, Integer> e : freq.entrySet()) {
            pq.offer(new int[] { e.getKey(), e.getValue() });
            if (pq.size() > k) pq.poll();
        }

        int[] ans = new int[k];
        for (int i = k - 1; i >= 0; i--) ans[i] = pq.poll()[0];
        return ans;
    }
}
```

</details>

<a id="2-min-heap-cpp"></a>
<details>
<summary><strong>C++</strong> — TC: O(n + m log k), SC: O(m + k) · <em>click to show / hide code</em></summary>

```cpp
#include <queue>
#include <unordered_map>
#include <vector>
using std::pair;
using std::priority_queue;
using std::unordered_map;
using std::vector;

class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        unordered_map<int, int> freq;
        for (int x : nums) freq[x]++;

        using P = pair<int, int>;
        priority_queue<P, vector<P>, greater<P>> pq;
        for (auto& p : freq) {
            pq.push({ p.second, p.first });
            if ((int)pq.size() > k) pq.pop();
        }

        vector<int> ans;
        while (!pq.empty()) {
            ans.push_back(pq.top().second);
            pq.pop();
        }
        return ans;
    }
};
```

</details>

<a id="2-min-heap-python"></a>
<details>
<summary><strong>Python</strong> — TC: O(n + m log k), SC: O(m + k) · <em>click to show / hide code</em></summary>

```python
import heapq
from collections import Counter

class Solution:
    def topKFrequent(self, nums: list[int], k: int) -> list[int]:
        freq = Counter(nums)
        heap: list[tuple[int, int]] = []
        for x, c in freq.items():
            heapq.heappush(heap, (c, x))
            if len(heap) > k:
                heapq.heappop(heap)
        return [x for _, x in heap]
```

</details>

---

## 3. Bucket Sort

### Intuition

Any frequency is between **1** and **`n`** (when all elements are equal). Create **`n + 1` buckets**, where bucket `f` holds **all values** whose frequency is exactly `f`. Then scan frequencies from **`n` down to `1`** and collect values until you have `k` answers—no sorting of distinct elements needed.

### Algorithm

1. Count frequencies.
2. Allocate `buckets[0..n]` (lists); for each `(value, f)`, append `value` to `buckets[f]`.
3. Iterate `f` from `n` down to `1`. For each non-empty `buckets[f]`, append elements to the answer until length is `k`.

### Time & Space Complexity

| | Time | Space |
|---|------|--------|
| **Bucket by frequency** | **O(n)** | **O(n)** |

<a id="3-bucket-sort-java"></a>
<details>
<summary><strong>Java</strong> — TC: O(n), SC: O(n) · <em>click to show / hide code</em></summary>

```java
import java.util.*;

class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> freq = new HashMap<>();
        for (int x : nums) freq.merge(x, 1, Integer::sum);

        List<Integer>[] buckets = new List[nums.length + 1];
        for (Map.Entry<Integer, Integer> e : freq.entrySet()) {
            int f = e.getValue();
            if (buckets[f] == null) buckets[f] = new ArrayList<>();
            buckets[f].add(e.getKey());
        }

        int[] ans = new int[k];
        int idx = 0;
        for (int f = buckets.length - 1; f >= 0 && idx < k; f--) {
            if (buckets[f] == null) continue;
            for (int x : buckets[f]) {
                ans[idx++] = x;
                if (idx == k) break;
            }
        }
        return ans;
    }
}
```

</details>

<a id="3-bucket-sort-cpp"></a>
<details>
<summary><strong>C++</strong> — TC: O(n), SC: O(n) · <em>click to show / hide code</em></summary>

```cpp
#include <unordered_map>
#include <vector>
using std::unordered_map;
using std::vector;

class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        unordered_map<int, int> freq;
        for (int x : nums) freq[x]++;

        vector<vector<int>> buckets(nums.size() + 1);
        for (auto& p : freq) buckets[p.second].push_back(p.first);

        vector<int> ans;
        for (int f = (int)buckets.size() - 1; f >= 0 && (int)ans.size() < k; f--) {
            for (int x : buckets[f]) {
                ans.push_back(x);
                if ((int)ans.size() == k) break;
            }
        }
        return ans;
    }
};
```

</details>

<a id="3-bucket-sort-python"></a>
<details>
<summary><strong>Python</strong> — TC: O(n), SC: O(n) · <em>click to show / hide code</em></summary>

```python
from collections import Counter

class Solution:
    def topKFrequent(self, nums: list[int], k: int) -> list[int]:
        freq = Counter(nums)
        buckets: list[list[int]] = [[] for _ in range(len(nums) + 1)]
        for x, c in freq.items():
            buckets[c].append(x)
        ans: list[int] = []
        for f in range(len(buckets) - 1, 0, -1):
            for x in buckets[f]:
                ans.append(x)
                if len(ans) == k:
                    return ans
        return ans
```

</details>

---

## Common Pitfalls

### Using a Max-Heap Instead of Min-Heap

- The usual pattern is a **min-heap of size `k` on frequency**: evict the **smallest** among the `k` candidates so the heap always holds the **`k` largest** frequencies.
- A **max-heap of all `m` elements** then popping `k` times is **O(m + k log m)**—valid but not the “small `k`” optimization. Confusing **comparator direction** (min vs max) leads to wrong elements being dropped.

### Forgetting to Handle Ties in Frequency

- **Output order** is often **unspecified**—any `k` values whose frequencies are among the top tier is fine.
- When several values share the **same** frequency, **bucket sort** puts them in the **same bucket**; you may return **any subset** of size `k` consistent with the problem’s guarantee (e.g. unique frequency ranking) or collect until `k` as in the algorithms above.
- **Heap tie-breaking:** if two pairs have the same frequency, either tie-break consistently or rely on “any order” for the result list.

### Off-By-One in Bucket Sort Index

- Maximum possible frequency is **`n`** (all elements identical), so buckets need indices **`0 .. n`**, i.e. **`n + 1` slots**. Using length **`n`** loses bucket `n` or misindexes highest frequency.
- Bucket **`0`** is unused for nonempty frequency maps (minimum frequency is at least `1`); loops can run **`f` from `n` down to `1`** or include `0` safely if empty.
