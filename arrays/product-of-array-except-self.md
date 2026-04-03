# Product of Array Except Self

## Navigate

Jump to a solution or section (click to scroll):

| | |
|--|--|
| **Overview** | [Description](#description) · [Prerequisites](#prerequisites) · [Common Pitfalls](#common-pitfalls) |
| **1. Brute Force** | [Section](#1-brute-force) — [Java](#1-brute-force-java) · [C++](#1-brute-force-cpp) · [Python](#1-brute-force-python) |
| **2. Division** | [Section](#2-division) — [Java](#2-division-java) · [C++](#2-division-cpp) · [Python](#2-division-python) |
| **3. Prefix & Suffix** | [Section](#3-prefix--suffix) — [Java](#3-prefix--suffix-java) · [C++](#3-prefix--suffix-cpp) · [Python](#3-prefix--suffix-python) |
| **4. Prefix & Suffix (Optimal)** | [Section](#4-prefix--suffix-optimal) — [Java](#4-prefix--suffix-optimal-java) · [C++](#4-prefix--suffix-optimal-cpp) · [Python](#4-prefix--suffix-optimal-python) |

**Tip:** Each code block is inside a collapsible panel—click the language row to show or hide the source.

---

## Description

Given an integer array `nums`, return an array `answer` such that `answer[i]` is the **product of all elements of `nums` except `nums[i]`**.

The product of **any** prefix or suffix of `nums` is **guaranteed** to fit in a 32-bit integer.

You must write an algorithm that runs in **`O(n)`** time and without using the **division** operation—**unless** you use a division-based approach as an intermediate step in this note (LeetCode 238 disallows division in the intended solution).

**Constraints (typical):** `2 <= nums.length <= 10^5`, `-30 <= nums[i] <= 30`.

## Prerequisites

- **Prefix / suffix products:** `answer[i]` equals (product of elements **before** `i`) × (product of elements **after** `i`).
- **Division shortcut:** total product divided by `nums[i]` **fails** when `nums[i] == 0` unless you count zeros explicitly.
- **Space trade-off:** two auxiliary arrays vs reusing the output array plus one running variable.

---

## 1. Brute Force

### Intuition

For each index `i`, multiply **every** element except `i`. No extra structure—just nested loops.

### Algorithm

1. Allocate `answer` of length `n`.
2. For each `i` from `0` to `n - 1`, set `answer[i] = 1`, then for each `j != i`, multiply `answer[i]` by `nums[j]`.
3. Return `answer`.

### Time & Space Complexity

| | Time | Space |
|---|------|--------|
| **Brute force** | **O(n²)** | **O(1)** extra * |

\* Excluding the required output array.

<a id="1-brute-force-java"></a>
<details>
<summary><strong>Java</strong> — TC: O(n²), SC: O(1) extra · <em>click to show / hide code</em></summary>

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

</details>

<a id="1-brute-force-cpp"></a>
<details>
<summary><strong>C++</strong> — TC: O(n²), SC: O(1) extra · <em>click to show / hide code</em></summary>

```cpp
#include <vector>
using std::vector;

class Solution {
public:
    vector<int> productExceptSelf(vector<int>& nums) {
        int n = (int)nums.size();
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

</details>

<a id="1-brute-force-python"></a>
<details>
<summary><strong>Python</strong> — TC: O(n²), SC: O(1) extra · <em>click to show / hide code</em></summary>

```python
class Solution:
    def productExceptSelf(self, nums: list[int]) -> list[int]:
        n = len(nums)
        ans = [0] * n
        for i in range(n):
            p = 1
            for j in range(n):
                if i != j:
                    p *= nums[j]
            ans[i] = p
        return ans
```

</details>

---

## 2. Division

### Intuition

If there were **no zeros**, `answer[i] = (product of all nums) / nums[i]`. Zeros break division: track **how many zeros** and the **product of non-zero** entries, then branch—`0`, `1`, or `2+` zeros determine each position.

### Algorithm

1. One pass: count `zeroCount`, compute `prod` = product of all **non-zero** values.
2. For each `i`:
   - If `zeroCount > 1`, every `answer[i] = 0`.
   - Else if `zeroCount == 1`, `answer[i] = prod` if `nums[i] == 0`, else `0`.
   - Else `answer[i] = prod / nums[i]`.

### Time & Space Complexity

| | Time | Space |
|---|------|--------|
| **Division + zero rules** | **O(n)** | **O(1)** extra * |

\* Excluding output; uses division (often disallowed for the “follow-up” on LeetCode).

<a id="2-division-java"></a>
<details>
<summary><strong>Java</strong> — TC: O(n), SC: O(1) extra · <em>click to show / hide code</em></summary>

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

</details>

<a id="2-division-cpp"></a>
<details>
<summary><strong>C++</strong> — TC: O(n), SC: O(1) extra · <em>click to show / hide code</em></summary>

```cpp
#include <vector>
using std::vector;

class Solution {
public:
    vector<int> productExceptSelf(vector<int>& nums) {
        int n = (int)nums.size(), zeroCount = 0, prod = 1;
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

</details>

<a id="2-division-python"></a>
<details>
<summary><strong>Python</strong> — TC: O(n), SC: O(1) extra · <em>click to show / hide code</em></summary>

```python
class Solution:
    def productExceptSelf(self, nums: list[int]) -> list[int]:
        zero_count = sum(1 for x in nums if x == 0)
        prod = 1
        for x in nums:
            if x != 0:
                prod *= x
        n = len(nums)
        ans = [0] * n
        for i in range(n):
            if zero_count > 1:
                ans[i] = 0
            elif zero_count == 1:
                ans[i] = prod if nums[i] == 0 else 0
            else:
                ans[i] = prod // nums[i]
        return ans
```

</details>

---

<a id="3-prefix--suffix"></a>

## 3. Prefix & Suffix

### Intuition

`answer[i]` = (product of elements **left** of `i`) × (product of elements **right** of `i`). Precompute **`prefix[i]`** = product of `nums[0..i-1]` and **`suffix[i]`** = product of `nums[i+1..n-1]`, then `answer[i] = prefix[i] * suffix[i]`. Clear structure, **O(n)** time, **O(n)** extra space for the two helper arrays.

### Algorithm

1. Build `prefix` of length `n`: `prefix[0] = 1`; for `i >= 1`, `prefix[i] = prefix[i-1] * nums[i-1]`.
2. Build `suffix` of length `n`: `suffix[n-1] = 1`; for `i` from `n-2` down to `0`, `suffix[i] = suffix[i+1] * nums[i+1]`.
3. For each `i`, `answer[i] = prefix[i] * suffix[i]`.

### Time & Space Complexity

| | Time | Space |
|---|------|--------|
| **Prefix + suffix arrays** | **O(n)** | **O(n)** extra |

<a id="3-prefix--suffix-java"></a>
<details>
<summary><strong>Java</strong> — TC: O(n), SC: O(n) extra · <em>click to show / hide code</em></summary>

```java
class Solution {
    public int[] productExceptSelf(int[] nums) {
        int n = nums.length;
        int[] prefix = new int[n];
        prefix[0] = 1;
        for (int i = 1; i < n; i++) prefix[i] = prefix[i - 1] * nums[i - 1];

        int[] suffix = new int[n];
        suffix[n - 1] = 1;
        for (int i = n - 2; i >= 0; i--) suffix[i] = suffix[i + 1] * nums[i + 1];

        int[] ans = new int[n];
        for (int i = 0; i < n; i++) ans[i] = prefix[i] * suffix[i];
        return ans;
    }
}
```

</details>

<a id="3-prefix--suffix-cpp"></a>
<details>
<summary><strong>C++</strong> — TC: O(n), SC: O(n) extra · <em>click to show / hide code</em></summary>

```cpp
#include <vector>
using std::vector;

class Solution {
public:
    vector<int> productExceptSelf(vector<int>& nums) {
        int n = (int)nums.size();
        vector<int> prefix(n, 1);
        for (int i = 1; i < n; i++) prefix[i] = prefix[i - 1] * nums[i - 1];

        vector<int> suffix(n, 1);
        for (int i = n - 2; i >= 0; i--) suffix[i] = suffix[i + 1] * nums[i + 1];

        vector<int> ans(n);
        for (int i = 0; i < n; i++) ans[i] = prefix[i] * suffix[i];
        return ans;
    }
};
```

</details>

<a id="3-prefix--suffix-python"></a>
<details>
<summary><strong>Python</strong> — TC: O(n), SC: O(n) extra · <em>click to show / hide code</em></summary>

```python
class Solution:
    def productExceptSelf(self, nums: list[int]) -> list[int]:
        n = len(nums)
        prefix = [1] * n
        for i in range(1, n):
            prefix[i] = prefix[i - 1] * nums[i - 1]

        suffix = [1] * n
        for i in range(n - 2, -1, -1):
            suffix[i] = suffix[i + 1] * nums[i + 1]

        return [prefix[i] * suffix[i] for i in range(n)]
```

</details>

---

<a id="4-prefix--suffix-optimal"></a>

## 4. Prefix & Suffix (Optimal)

### Intuition

The prefix array can be **written directly into `answer`**: after the left pass, `answer[i]` holds the product of all elements to the **left** of `i`. A second pass from the **right** keeps a running **suffix** product and multiplies it into `answer[i]`. Only **one** extra scalar—**O(1)** auxiliary space (besides output).

### Algorithm

1. Set `answer[0] = 1`. For `i` from `1` to `n-1`, `answer[i] = answer[i-1] * nums[i-1]` (prefix products).
2. Set `suffix = 1`. For `i` from `n-1` down to `0`: `answer[i] *= suffix`, then `suffix *= nums[i]`.

### Time & Space Complexity

| | Time | Space |
|---|------|--------|
| **Output as prefix + suffix run** | **O(n)** | **O(1)** extra * |

\* Excluding the output array.

<a id="4-prefix--suffix-optimal-java"></a>
<details>
<summary><strong>Java</strong> — TC: O(n), SC: O(1) extra · <em>click to show / hide code</em></summary>

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

</details>

<a id="4-prefix--suffix-optimal-cpp"></a>
<details>
<summary><strong>C++</strong> — TC: O(n), SC: O(1) extra · <em>click to show / hide code</em></summary>

```cpp
#include <vector>
using std::vector;

class Solution {
public:
    vector<int> productExceptSelf(vector<int>& nums) {
        int n = (int)nums.size();
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

</details>

<a id="4-prefix--suffix-optimal-python"></a>
<details>
<summary><strong>Python</strong> — TC: O(n), SC: O(1) extra · <em>click to show / hide code</em></summary>

```python
class Solution:
    def productExceptSelf(self, nums: list[int]) -> list[int]:
        n = len(nums)
        ans = [1] * n
        for i in range(1, n):
            ans[i] = ans[i - 1] * nums[i - 1]
        suffix = 1
        for i in range(n - 1, -1, -1):
            ans[i] *= suffix
            suffix *= nums[i]
        return ans
```

</details>

---

## Common Pitfalls

### Using Division Without Handling Zeros

- **`prod / nums[i]`** is undefined when `nums[i] == 0` and misleading when multiple zeros exist. Use **zero counts** (`0`, `1`, or `≥ 2`) or **avoid division** with prefix/suffix products.

### Off-by-One Errors in Prefix/Suffix Array Construction

- **Prefix:** `prefix[i]` usually means product of elements **before** `i`, so `prefix[0] = 1` and `prefix[i] = prefix[i-1] * nums[i-1]` for `i ≥ 1`.
- **Suffix:** `suffix[i]` is product of elements **after** `i`; initialize `suffix[n-1] = 1` and walk **downward** with `suffix[i] = suffix[i+1] * nums[i+1]`.
- In the **optimal** variant, the left pass fills “prefix into `answer`”; the right pass must multiply **`suffix` before** updating `suffix *= nums[i]` so you do not include `nums[i]` twice.

### Integer Overflow with Large Products

- Intermediate products can exceed 32-bit range in **general** arrays; LeetCode 238 **guarantees** 32-bit fits for prefix/suffix products in the test data—still be aware in **other** contexts or languages without big integers.
- In Python, arbitrary-length ints hide overflow; in Java/C++, use **`long`** for intermediates if the problem allows larger ranges.
