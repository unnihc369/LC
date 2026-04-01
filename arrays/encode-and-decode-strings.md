# Encode and Decode Strings

Design an algorithm to encode a list of strings to a single string and decode it back.

## Approach 1: Brute Force (Delimiter Join)
- Join strings by a delimiter like `|`.
- Fails when input string itself contains delimiter.
- Time: `O(totalChars)`, but not robust.

### Java
```java
import java.util.*;

class Codec {
    public String encode(List<String> strs) {
        return String.join("|", strs); // Not safe if strings contain "|"
    }

    public List<String> decode(String s) {
        if (s.isEmpty()) return new ArrayList<>();
        return Arrays.asList(s.split("\\|", -1));
    }
}
```

### C++
```cpp
class Codec {
public:
    string encode(vector<string>& strs) {
        string out;
        for (int i = 0; i < (int)strs.size(); i++) {
            if (i) out += "|";
            out += strs[i];
        }
        return out; // Not safe if input contains "|"
    }

    vector<string> decode(string s) {
        vector<string> ans;
        string cur;
        for (char c : s) {
            if (c == '|') {
                ans.push_back(cur);
                cur.clear();
            } else cur.push_back(c);
        }
        if (!s.empty()) ans.push_back(cur);
        return ans;
    }
};
```

## Approach 2: Better (Escape + Delimiter)
- Use delimiter but escape delimiter occurrences inside strings.
- More complex and error-prone to implement.
- Time: `O(totalChars)`, Space: `O(totalChars)`.

### Java
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
            } else cur.append(c);
        }
        return ans;
    }
}
```

### C++
```cpp
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
            } else cur.push_back(c);
        }
        return ans;
    }
};
```

## Approach 3: Optimal (Length Prefix)
- Encode each string as: `length#string`.
- During decode, parse length, then take exact number of characters.
- Works for all characters safely.
- Time: `O(totalChars)`, Space: `O(totalChars)`.

### Java
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

### C++
```cpp
class Codec {
public:
    string encode(vector<string>& strs) {
        string out;
        for (string& s : strs) {
            out += to_string(s.size()) + "#" + s;
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
