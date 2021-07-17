---
title: 有效的数独
categories:
  - LeetCode
  - 初级算法
  - 数组
tags:
  - C++
  - 数组
  - 哈希表
index_img: /img/code.jpg
date: 2021-07-16 16:20:49
---

# 有效的数独

https://leetcode-cn.com/leetbook/read/top-interview-questions-easy/x2f9gg/

https://leetcode-cn.com/problems/valid-sudoku

## 我的方法

容易想到的是使用哈希表来记录一行/一列/一个方格内是否出现了重复值。

```c++
bool isValidSudoku(vector<vector<char>>& board) {
    int n = board.size();
    unordered_set<char> row, col, grid;

    for (int i = 0; i < n; ++i) {
        for (int j = 0; j < n; ++j) {
            if (board[i][j] != '.' && !(row.insert(board[i][j]).second)) {
                return false;
            }
            if (board[j][i] != '.' && !(col.insert(board[j][i]).second)) {
                return false;
            }
            if (i%3==0 && j%3==0) {
                for (int x = 0; x < 3; ++x) {
                    for (int y = 0; y < 3; ++y) {
                        if (board[i+x][j+y] != '.' && !(grid.insert(board[i+x][j+y]).second)) {
                            return false;
                        }
                    }
                }
            }
            grid.clear();
        }
        row.clear();
        col.clear();
    }

    return true;
}
```

每一行和每一列是否有重复值比较好搞，但是如何在一次遍历中就记录方格里的值呢？

我的方法是判断遍历到的值是不是方格左上角的元素，是的话再遍历整个方格。这样实际上多了不必要的访问。

## 更好的方法

如何在一次遍历就访问到方格里的元素呢？大家可能很好奇如何在一次遍历就访问到方格里的元素。方法就是

```c++
int m = j / 3 + i / 3 * 3;
int n = j % 3 + i % 3 * 3;
```

小编也很奇怪，为什么可以这样呢？

```c++
bool isValidSudoku(vector<vector<char>>& board) {
    int n = board.size();
    unordered_set<char> row, col, grid;

    for (int i = 0; i < n; ++i) {
        for (int j = 0; j < n; ++j) {
            if (board[i][j] != '.' && !(row.insert(board[i][j]).second)) {
                return false;
            }
            if (board[j][i] != '.' && !(col.insert(board[j][i]).second)) {
                return false;
            }

            int m = j / 3 + i / 3 * 3;
            int n = j % 3 + i % 3 * 3;
            if (board[m][n] != '.' && !(grid.insert(board[m][n]).second)) {
                return false;
            }
        }
        row.clear();
        col.clear();
        grid.clear();
    }

    return true;
}
```

