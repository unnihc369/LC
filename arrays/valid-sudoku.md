# Valid Sudoku

Determine if a 9x9 Sudoku board is valid according to row, column, and 3x3 box rules.

## Approach 1: Brute Force
- For every filled cell, scan row, column, and box for duplicate.
- Time: `O(9^3)` (constant for fixed 9x9), Space: `O(1)`.

### Java
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

### C++
```cpp
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

## Approach 2: Better (Three Frequency Tables)
- Use `rows[9][9]`, `cols[9][9]`, `boxes[9][9]`.
- Check and mark in O(1) per cell.
- Time: `O(81)`, Space: `O(1)` (fixed size).

### Java
```java
class Solution {
    public boolean isValidSudoku(char[][] board) {
        int[][] rows = new int[9][9];
        int[][] cols = new int[9][9];
        int[][] boxes = new int[9][9];

        for (int r = 0; r < 9; r++) {
            for (int c = 0; c < 9; c++) {
                if (board[r][c] == '.') continue;
                int d = board[r][c] - '1';
                int b = (r / 3) * 3 + (c / 3);
                if (rows[r][d]++ > 0 || cols[c][d]++ > 0 || boxes[b][d]++ > 0) return false;
            }
        }
        return true;
    }
}
```

### C++
```cpp
class Solution {
public:
    bool isValidSudoku(vector<vector<char>>& board) {
        int rows[9][9] = {}, cols[9][9] = {}, boxes[9][9] = {};
        for (int r = 0; r < 9; r++) {
            for (int c = 0; c < 9; c++) {
                if (board[r][c] == '.') continue;
                int d = board[r][c] - '1';
                int b = (r / 3) * 3 + (c / 3);
                if (rows[r][d]++ > 0 || cols[c][d]++ > 0 || boxes[b][d]++ > 0) return false;
            }
        }
        return true;
    }
};
```

## Approach 3: Optimal (Bitmask)
- Use bitmasks for rows, cols, and boxes.
- Check/set bits for each digit in O(1).
- Time: `O(81)`, Space: `O(1)`.

### Java
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

### C++
```cpp
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
