# Valid Sudoku

## Navigate

Jump to a solution or section (click to scroll):

| | |
|--|--|
| **Overview** | [Description](#description) · [Prerequisites](#prerequisites) · [Common Pitfalls](#common-pitfalls) |
| **1. Brute Force** | [Section](#1-brute-force) — [Java](#1-brute-force-java) · [C++](#1-brute-force-cpp) · [Python](#1-brute-force-python) |
| **2. Hash Set (One Pass)** | [Section](#2-hash-set-one-pass) — [Java](#2-hash-set-one-pass-java) · [C++](#2-hash-set-one-pass-cpp) · [Python](#2-hash-set-one-pass-python) |
| **3. Bitmask** | [Section](#3-bitmask) — [Java](#3-bitmask-java) · [C++](#3-bitmask-cpp) · [Python](#3-bitmask-python) |

**Tip:** Each code block is inside a collapsible panel—click the language row to show or hide the source.

---

## Description

Determine if a **9 × 9** Sudoku board is **valid** according to Sudoku rules:

- Each **row** must contain digits `1`–`9` without repetition (among filled cells).
- Each **column** must contain digits `1`–`9` without repetition.
- Each of the nine **3 × 3 sub-boxes** must contain digits `1`–`9` without repetition.

Only the **filled** cells need to satisfy these constraints; `'.'` denotes an empty cell. **Partially filled** boards may still be valid.

**Constraints:** `board.length == 9`, `board[i].length == 9`, cells are `'.'` or `'1'`–`'9'`.

## Prerequisites

- **Grid indexing:** `0 ≤ r, c < 9` for rows and columns.
- **Box index:** map `(r, c)` to box id **`(r / 3) * 3 + (c / 3)`** (integer division).
- **Sets / bits:** track which digits already appeared per row, column, or box.
- **Fixed board size:** asymptotics are **O(1)** for constants, but **81 cells** is the work unit.

---

## 1. Brute Force

### Intuition

For **each** non-empty cell, **rescan** its row, column, and 3 × 3 box and ensure no other cell has the same digit. No extra storage; repeated scans are redundant but simple.

### Algorithm

1. For each `(r, c)` in `0..8 × 0..8`, if `board[r][c] == '.'`, **continue**.
2. Let `ch = board[r][c]`. For each `k` in `0..8`: if another cell in **row** `r` matches `ch`, return `false`; if another in **column** `c` matches `ch`, return `false`.
3. Compute box top-left `(br, bc) = (r/3*3, c/3*3)`, scan the 3 × 3 region; if any cell (other than `(r,c)`) equals `ch`, return `false`.
4. If no violation, return `true`.

### Time & Space Complexity

| | Time | Space |
|---|------|--------|
| **Brute force** | **O(n²)** * / **O(1)** for fixed 9×9 | **O(1)** |

\* `n = 9`; roughly `81 × (9 + 9 + 9)` checks.

<a id="1-brute-force-java"></a>
<details>
<summary><strong>Java</strong> — TC: O(1) fixed board, SC: O(1) · <em>click to show / hide code</em></summary>

```java
class Solution {
    public boolean isValidSudoku(char[][] board) {
        for (int r = 0; r < 9; r++) {
            for (int c = 0; c < 9; c++) {
                char ch = board[r][c];
                if (ch == '.') continue;
                for (int k = 0; k < 9; k++) {
                    if (k != c && board[r][k] == ch) return false;
                    if (k != r && board[k][c] == ch) return false;
                }
                int br = (r / 3) * 3, bc = (c / 3) * 3;
                for (int i = br; i < br + 3; i++) {
                    for (int j = bc; j < bc + 3; j++) {
                        if ((i != r || j != c) && board[i][j] == ch) return false;
                    }
                }
            }
        }
        return true;
    }
}
```

</details>

<a id="1-brute-force-cpp"></a>
<details>
<summary><strong>C++</strong> — TC: O(1) fixed board, SC: O(1) · <em>click to show / hide code</em></summary>

```cpp
#include <vector>
using std::vector;

class Solution {
public:
    bool isValidSudoku(vector<vector<char>>& board) {
        for (int r = 0; r < 9; r++) {
            for (int c = 0; c < 9; c++) {
                char ch = board[r][c];
                if (ch == '.') continue;
                for (int k = 0; k < 9; k++) {
                    if (k != c && board[r][k] == ch) return false;
                    if (k != r && board[k][c] == ch) return false;
                }
                int br = (r / 3) * 3, bc = (c / 3) * 3;
                for (int i = br; i < br + 3; i++) {
                    for (int j = bc; j < bc + 3; j++) {
                        if ((i != r || j != c) && board[i][j] == ch) return false;
                    }
                }
            }
        }
        return true;
    }
};
```

</details>

<a id="1-brute-force-python"></a>
<details>
<summary><strong>Python</strong> — TC: O(1) fixed board, SC: O(1) · <em>click to show / hide code</em></summary>

```python
class Solution:
    def isValidSudoku(self, board: list[list[str]]) -> bool:
        for r in range(9):
            for c in range(9):
                ch = board[r][c]
                if ch == ".":
                    continue
                for k in range(9):
                    if k != c and board[r][k] == ch:
                        return False
                    if k != r and board[k][c] == ch:
                        return False
                br, bc = (r // 3) * 3, (c // 3) * 3
                for i in range(br, br + 3):
                    for j in range(bc, bc + 3):
                        if (i != r or j != c) and board[i][j] == ch:
                            return False
        return True
```

</details>

---

<a id="2-hash-set-one-pass"></a>

## 2. Hash Set (One Pass)

### Intuition

Traverse the board **once**. For each filled cell, record that digit appears in **this row**, **this column**, and **this box** using **unique keys** in one `HashSet`. If any `add` returns `false` (key already present), a rule is violated.

### Algorithm

1. Create an empty `Set` of strings (or encoded keys).
2. For each `(r, c)`, if `board[r][c] == '.'`, **continue**.
3. Let `ch` be the digit, `b = (r / 3) * 3 + (c / 3)` the box id.
4. Insert three keys, e.g. **row–digit** `"r" + r + ch`, **column–digit** `"c" + c + ch`, **box–digit** `"b" + b + ch`. If any insert fails, return `false`.
5. After the loop, return `true`.

### Time & Space Complexity

| | Time | Space |
|---|------|--------|
| **One pass + hash set** | **O(81)** | **O(1)** * |

\* At most a constant number of keys (≤ 3 × 81) for a 9×9 board.

<a id="2-hash-set-one-pass-java"></a>
<details>
<summary><strong>Java</strong> — TC: O(1), SC: O(1) · <em>click to show / hide code</em></summary>

```java
import java.util.HashSet;
import java.util.Set;

class Solution {
    public boolean isValidSudoku(char[][] board) {
        Set<String> seen = new HashSet<>();
        for (int r = 0; r < 9; r++) {
            for (int c = 0; c < 9; c++) {
                if (board[r][c] == '.') continue;
                char ch = board[r][c];
                int b = (r / 3) * 3 + (c / 3);
                String rowKey = "r" + r + ch;
                String colKey = "c" + c + ch;
                String boxKey = "b" + b + ch;
                if (!seen.add(rowKey) || !seen.add(colKey) || !seen.add(boxKey)) return false;
            }
        }
        return true;
    }
}
```

</details>

<a id="2-hash-set-one-pass-cpp"></a>
<details>
<summary><strong>C++</strong> — TC: O(1), SC: O(1) · <em>click to show / hide code</em></summary>

```cpp
#include <string>
#include <unordered_set>
#include <vector>
using std::string;
using std::unordered_set;
using std::vector;

class Solution {
public:
    bool isValidSudoku(vector<vector<char>>& board) {
        unordered_set<string> seen;
        for (int r = 0; r < 9; r++) {
            for (int c = 0; c < 9; c++) {
                if (board[r][c] == '.') continue;
                char ch = board[r][c];
                int b = (r / 3) * 3 + (c / 3);
                string rowKey = "r" + to_string(r) + ch;
                string colKey = "c" + to_string(c) + ch;
                string boxKey = "b" + to_string(b) + ch;
                if (!seen.insert(rowKey).second || !seen.insert(colKey).second || !seen.insert(boxKey).second)
                    return false;
            }
        }
        return true;
    }
};
```

</details>

<a id="2-hash-set-one-pass-python"></a>
<details>
<summary><strong>Python</strong> — TC: O(1), SC: O(1) · <em>click to show / hide code</em></summary>

```python
class Solution:
    def isValidSudoku(self, board: list[list[str]]) -> bool:
        seen: set[tuple[object, ...]] = set()
        for r in range(9):
            for c in range(9):
                ch = board[r][c]
                if ch == ".":
                    continue
                b = (r // 3) * 3 + (c // 3)
                for key in ((0, r, ch), (1, c, ch), (2, b, ch)):
                    if key in seen:
                        return False
                    seen.add(key)
        return True
```

</details>

---

## 3. Bitmask

### Intuition

Each row, column, and box only needs to remember which of **nine** digits have appeared—use a **9-bit integer** per row, per column, and per box. For digit `d` (0-based from `'1'`), use `bit = 1 << d`. Before setting, check **`(mask & bit) != 0`** for a duplicate; then **`mask |= bit`**.

### Algorithm

1. Allocate `rows[9]`, `cols[9]`, `boxes[9]`, initialized to `0`.
2. For each cell, skip `'.'`. Compute `bit = 1 << (ch - '1')`, **`b = (r/3)*3 + (c/3)`**.
3. If `(rows[r] & bit) != 0` or same for `cols[c]` or `boxes[b]`, return `false`.
4. Otherwise OR `bit` into all three masks.
5. Return `true` after scanning all cells.

### Time & Space Complexity

| | Time | Space |
|---|------|--------|
| **Bitmask** | **O(81)** | **O(1)** |

<a id="3-bitmask-java"></a>
<details>
<summary><strong>Java</strong> — TC: O(1), SC: O(1) · <em>click to show / hide code</em></summary>

```java
class Solution {
    public boolean isValidSudoku(char[][] board) {
        int[] rows = new int[9], cols = new int[9], boxes = new int[9];
        for (int r = 0; r < 9; r++) {
            for (int c = 0; c < 9; c++) {
                if (board[r][c] == '.') continue;
                int bit = 1 << (board[r][c] - '1');
                int b = (r / 3) * 3 + (c / 3);
                if ((rows[r] & bit) != 0 || (cols[c] & bit) != 0 || (boxes[b] & bit) != 0) return false;
                rows[r] |= bit;
                cols[c] |= bit;
                boxes[b] |= bit;
            }
        }
        return true;
    }
}
```

</details>

<a id="3-bitmask-cpp"></a>
<details>
<summary><strong>C++</strong> — TC: O(1), SC: O(1) · <em>click to show / hide code</em></summary>

```cpp
#include <vector>
using std::vector;

class Solution {
public:
    bool isValidSudoku(vector<vector<char>>& board) {
        int rows[9] = {}, cols[9] = {}, boxes[9] = {};
        for (int r = 0; r < 9; r++) {
            for (int c = 0; c < 9; c++) {
                if (board[r][c] == '.') continue;
                int bit = 1 << (board[r][c] - '1');
                int b = (r / 3) * 3 + (c / 3);
                if ((rows[r] & bit) || (cols[c] & bit) || (boxes[b] & bit)) return false;
                rows[r] |= bit;
                cols[c] |= bit;
                boxes[b] |= bit;
            }
        }
        return true;
    }
};
```

</details>

<a id="3-bitmask-python"></a>
<details>
<summary><strong>Python</strong> — TC: O(1), SC: O(1) · <em>click to show / hide code</em></summary>

```python
class Solution:
    def isValidSudoku(self, board: list[list[str]]) -> bool:
        rows = [0] * 9
        cols = [0] * 9
        boxes = [0] * 9
        for r in range(9):
            for c in range(9):
                ch = board[r][c]
                if ch == ".":
                    continue
                bit = 1 << (ord(ch) - ord("1"))
                b = (r // 3) * 3 + (c // 3)
                if rows[r] & bit or cols[c] & bit or boxes[b] & bit:
                    return False
                rows[r] |= bit
                cols[c] |= bit
                boxes[b] |= bit
        return True
```

</details>

---

## Common Pitfalls

### Wrong Box Index Calculation

- Box id must be **`(r / 3) * 3 + (c / 3)`** using **integer division** (`//` in Python), mapping `0..8` to the nine 3×3 blocks. Mistakes like **`r % 3 * 3 + c % 3`** without scaling **`r` and `c` by `3`** pick the **cell inside** the box, not the box label **0–8**.

### Not Skipping Empty Cells

- **`'.'`** is not a digit; treating it like `'1'`–`'9'` creates **false duplicates** or invalid bit shifts. **Always** `continue` (or equivalent) for empty cells in every approach.
