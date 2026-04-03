# Encode and Decode Strings

## Navigate

Jump to a solution or section (click to scroll):

| | |
|--|--|
| **Overview** | [Description](#description) · [Prerequisites](#prerequisites) · [Common Pitfalls](#common-pitfalls) |
| **1. Encoding & Decoding** | [Section](#1-encoding--decoding) — [Java](#1-encoding--decoding-java) · [C++](#1-encoding--decoding-cpp) · [Python](#1-encoding--decoding-python) |
| **2. Encoding & Decoding (Optimal)** | [Section](#2-encoding--decoding-optimal) — [Java](#2-encoding--decoding-optimal-java) · [C++](#2-encoding--decoding-optimal-cpp) · [Python](#2-encoding--decoding-optimal-python) |

**Tip:** Each code block is inside a collapsible panel—click the language row to show or hide the source.

---

## Description

Design an algorithm to **encode** a list of strings into a **single** string and **decode** it back to the **original list** (same order, same contents).

The encoding must be **reversible** for **any** strings, including those that contain delimiters, newlines, Unicode, or binary-like data—so a naive “join with a single character” is not enough unless inputs are restricted.

**Constraints (typical):** `0 <= strs.length`, total characters bounded by problem statement; each string may be empty; characters are often arbitrary (treat as UTF-16 code units in Java, `char` in C++, Unicode in Python 3).

## Prerequisites

- **Strings and indexing:** random access by index, `substring` / `substr`, building with `StringBuilder` / `string` append.
- **Parsing:** read tokens from a stream; distinguish **metadata** (lengths, escapes) from **payload**.
- **Big-O:** total work is linear in **total characters** for sensible schemes—aim for **O(total chars)** time and output size.

---

<a id="1-encoding--decoding"></a>

## 1. Encoding & Decoding

### Intuition

A fixed **delimiter** alone fails when a string contains that character. **Escape** problematic bytes: e.g. prepend `\` before `\` and before the record delimiter `|`, then append `|` after each original string. Decoding is a **single left-to-right scan** with an escape flag—no length field, but output may grow when inputs contain many backslashes or delimiters.

### Algorithm

#### Encoding

1. For each string in order, scan each character `c`.
2. If `c` is `\` or the delimiter `|`, append `\` then `c`; otherwise append `c`.
3. After each string’s characters, append the delimiter `|` to mark the end of that record.
4. Concatenate all pieces into one encoded string.

#### Decoding

1. Walk the encoded string with an **escape flag** (initially false).
2. If the flag is set, append the current character literally and clear the flag.
3. Else if the character is `\`, set the flag (next character is literal).
4. Else if the character is `|`, push the current buffer as one decoded string and clear the buffer.
5. Else append the character to the buffer.
6. After the loop, return the list of strings (the trailing `|` after each record yields boundaries; empty list encodes to `""` with no records—see pitfalls).

### Time & Space Complexity

| | Time | Space |
|---|------|--------|
| **Escape + delimiter** | **O(total chars)** * | **O(total output)** |

\* Each input character is handled in constant amortized time; escaped characters expand the encoded size.

<a id="1-encoding--decoding-java"></a>
<details>
<summary><strong>Java</strong> — TC: O(total chars), SC: O(total output) · <em>click to show / hide code</em></summary>

```java
import java.util.*;

class Codec {
    public String encode(List<String> strs) {
        StringBuilder sb = new StringBuilder();
        for (String s : strs) {
            for (char c : s.toCharArray()) {
                if (c == '\\' || c == '|') sb.append('\\');
                sb.append(c);
            }
            sb.append('|');
        }
        return sb.toString();
    }

    public List<String> decode(String s) {
        List<String> ans = new ArrayList<>();
        StringBuilder cur = new StringBuilder();
        boolean esc = false;
        for (char c : s.toCharArray()) {
            if (esc) {
                cur.append(c);
                esc = false;
            } else if (c == '\\') {
                esc = true;
            } else if (c == '|') {
                ans.add(cur.toString());
                cur.setLength(0);
            } else {
                cur.append(c);
            }
        }
        return ans;
    }
}
```

</details>

<a id="1-encoding--decoding-cpp"></a>
<details>
<summary><strong>C++</strong> — TC: O(total chars), SC: O(total output) · <em>click to show / hide code</em></summary>

```cpp
#include <string>
#include <vector>
using std::string;
using std::vector;

class Codec {
public:
    string encode(vector<string>& strs) {
        string out;
        for (string& s : strs) {
            for (char c : s) {
                if (c == '\\' || c == '|') out.push_back('\\');
                out.push_back(c);
            }
            out.push_back('|');
        }
        return out;
    }

    vector<string> decode(string s) {
        vector<string> ans;
        string cur;
        bool esc = false;
        for (char c : s) {
            if (esc) {
                cur.push_back(c);
                esc = false;
            } else if (c == '\\') {
                esc = true;
            } else if (c == '|') {
                ans.push_back(cur);
                cur.clear();
            } else {
                cur.push_back(c);
            }
        }
        return ans;
    }
};
```

</details>

<a id="1-encoding--decoding-python"></a>
<details>
<summary><strong>Python</strong> — TC: O(total chars), SC: O(total output) · <em>click to show / hide code</em></summary>

```python
class Codec:
    def encode(self, strs: list[str]) -> str:
        parts: list[str] = []
        for s in strs:
            escaped = []
            for c in s:
                if c in "\\|":
                    escaped.append("\\")
                escaped.append(c)
            parts.append("".join(escaped) + "|")
        return "".join(parts)

    def decode(self, s: str) -> list[str]:
        ans: list[str] = []
        cur: list[str] = []
        esc = False
        for c in s:
            if esc:
                cur.append(c)
                esc = False
            elif c == "\\":
                esc = True
            elif c == "|":
                ans.append("".join(cur))
                cur = []
            else:
                cur.append(c)
        return ans
```

</details>

---

<a id="2-encoding--decoding-optimal"></a>

## 2. Encoding & Decoding (Optimal)

### Intuition

Prefix each payload with its **length in decimal** and a separator `#`, then append the raw string. Decoding reads digits until `#`, parses the integer, then copies **exactly** that many characters—no escaping, **no ambiguity** when the payload contains `#` or any other character. One linear pass, predictable size, and the usual interview solution.

### Algorithm

#### Encoding

1. For each string `s`, append `len(s)` as decimal digits, then `'#'`, then `s` itself.
2. Concatenate all blocks in list order into one string.

#### Decoding

1. Let `i = 0`, `n = len(encoded)`.
2. While `i < n`: scan forward from `i` until `'#'` at position `j`; parse `len = int(encoded[i:j])`.
3. The next string is `encoded[j+1 : j+1+len]` (exactly `len` code units).
4. Set `i = j + 1 + len` and repeat.

### Time & Space Complexity

| | Time | Space |
|---|------|--------|
| **Length prefix** | **O(total chars)** | **O(total output)** |

<a id="2-encoding--decoding-optimal-java"></a>
<details>
<summary><strong>Java</strong> — TC: O(total chars), SC: O(total output) · <em>click to show / hide code</em></summary>

```java
import java.util.*;

class Codec {
    public String encode(List<String> strs) {
        StringBuilder sb = new StringBuilder();
        for (String s : strs) {
            sb.append(s.length()).append('#').append(s);
        }
        return sb.toString();
    }

    public List<String> decode(String s) {
        List<String> ans = new ArrayList<>();
        int i = 0;
        while (i < s.length()) {
            int j = i;
            while (s.charAt(j) != '#') j++;
            int len = Integer.parseInt(s.substring(i, j));
            String word = s.substring(j + 1, j + 1 + len);
            ans.add(word);
            i = j + 1 + len;
        }
        return ans;
    }
}
```

</details>

<a id="2-encoding--decoding-optimal-cpp"></a>
<details>
<summary><strong>C++</strong> — TC: O(total chars), SC: O(total output) · <em>click to show / hide code</em></summary>

```cpp
#include <string>
#include <vector>
using std::string;
using std::vector;

class Codec {
public:
    string encode(vector<string>& strs) {
        string out;
        for (string& s : strs) {
            out += std::to_string(s.size());
            out.push_back('#');
            out += s;
        }
        return out;
    }

    vector<string> decode(string s) {
        vector<string> ans;
        int i = 0, n = (int)s.size();
        while (i < n) {
            int j = i;
            while (s[j] != '#') j++;
            int len = stoi(s.substr(i, j - i));
            string word = s.substr(j + 1, len);
            ans.push_back(word);
            i = j + 1 + len;
        }
        return ans;
    }
};
```

</details>

<a id="2-encoding--decoding-optimal-python"></a>
<details>
<summary><strong>Python</strong> — TC: O(total chars), SC: O(total output) · <em>click to show / hide code</em></summary>

```python
class Codec:
    def encode(self, strs: list[str]) -> str:
        return "".join(f"{len(s)}#{s}" for s in strs)

    def decode(self, s: str) -> list[str]:
        ans: list[str] = []
        i = 0
        n = len(s)
        while i < n:
            j = i
            while s[j] != "#":
                j += 1
            length = int(s[i:j])
            word = s[j + 1 : j + 1 + length]
            ans.append(word)
            i = j + 1 + length
        return ans
```

</details>

---

## Common Pitfalls

### Using a Delimiter That Can Appear in the Strings

- **Naive join** (e.g. `String.join("|", strs)`) breaks when any string contains `|`. You must **escape** reserved characters, use a **length prefix**, or restrict the input alphabet.
- **Escape schemes** must treat both `\` and the delimiter as special; forgetting to escape **backslash** allows ambiguous encodings.

### Not Handling Empty Strings or Empty Lists

- **Empty list:** `encode([])` is often `""`; `decode("")` must return **`[]`**, not `[""]`. Naive split/join can confuse “no strings” with “one empty string” unless the format defines record boundaries unambiguously (length-prefix handles `0#` for an empty string; escape format uses trailing `|` per record).
- **Empty string in the list:** a block **`0#`** decodes to `""`; parsers must allow **zero-length** slices and advance `i` by `j + 1 + 0`.

### Parsing Length Incorrectly for Multi-Digit Numbers

- Lengths like **`12#`** require reading **all digits** until `#`, then `Integer.parseInt` / `stoi` / `int(...)` on that substring—not a single digit.
- After `len` is known, take **exactly** `len` characters after `#`. Do not find the “next `#`” inside the payload—`#` may appear in the string body.
- **Off-by-one:** the payload starts at index **`j + 1`** and ends at **`j + len`** (exclusive end `j + 1 + len`); mixing up `i`, `j`, or slice bounds drops or duplicates characters.
