---
title:
  - '[LeetCode] #79 Word Search'
categories: software/leetcode
hide: true
summary: 二維陣列中某字串是否存在
date: 2020-07-23 11:50:44
---

## Problem

二維陣列中某字串是否存在

* [Link](https://leetcode.com/problems/word-search/)
* 等級：**Medium**
* 推薦指數：[:star::star::star::star:] 想法不難，但要成功寫實作出來有可能會卡住

> :star: 有人推薦過的題目的我才會紀錄，所以即使我覺得只有一顆星他依舊是一題有其他人推薦的題目，只是我自己不覺得需要刷
> :star::star: 代表我覺得有時間再看就好
> :star::star::star: 代表可以刷
> :star::star::star::star: 代表一定刷
> :star::star::star::star::star: 代表多刷幾次甚至把所有 solution 都弄懂

## 想法

就是採用 search 中的 **DFS 深度優先**，意思就是儘可能地先去耗盡 `word`，其實想法沒什麼特別的，也滿直觀的
只不過實作上有一個可以節省空間的技巧，就是可以將走過的地方『暫時』置換成空值以避免重複被走到
這當中可能包含同時要意識到 `board` 的 stack memory 在各 recursion 之間是什麼關係

## Source Code (Python)

``` python
class Solution:
    def exist(self, board: List[List[str]], word: str) -> bool:
        h = len(board)
        w = len(board[0])

        def _exist(x, y, word): # 定義清楚這個 function 提供的服務是實作過程的關鍵
            if not word:
                return True

            if board[x][y] != word[0]:
                return False
            elif len(word) == 1:
                return True

            board[x][y] = "" # 這裏的運用是關鍵

            if y+1 < w: # right
                if _exist(x, y+1, word[1:]):
                    return True
            if x+1 < h: # down
                if _exist(x+1, y, word[1:]):
                    return True
            if y-1 >= 0: # left
                if _exist(x, y-1, word[1:]):
                    return True
            if x-1 >= 0: # up
                if _exist(x-1, y, word[1:]):
                    return True

            board[x][y] = word[0] # 這裏的運用是關鍵

            return False

        for i in range(h):
            for j in range(w):
                if _exist(i, j, word):
                    return True

        return False
```

上面出現的 `board` 都是同一塊記憶體，沒有發生任何的 clone
時間順序上因為我們是 DFS，而且是 single thread，所以每次參照到 `board` 時都會是一個該輪恰當的狀態，恰當指的是哪裡該暫時置為空

## Source Code (Go)

``` go
func exist(board [][]byte, word string) bool {
    for i := 0; i < len(board); i++ {
        for j := 0; j < len(board[0]); j++ {
            if _exist(i, j, word, board) {
                return true
            }
        }
    }

    return false
}

func _exist(x int, y int, word string, board [][]byte) bool {
    if len(word) == 0 {
        return true
    }

    if board[x][y] != word[0] {
        return false
    } else if len(word) == 1 {
        return true
    }

    board[x][y] = ' '

    if y+1 < len(board[0]) { // right
        if _exist(x, y+1, word[1:], board) {
            return true
        }
    }
    if x+1 < len(board) { // down
        if _exist(x+1, y, word[1:], board) {
            return true
        }
    }
    if y-1 >= 0 { // left
        if _exist(x, y-1, word[1:], board) {
            return true
        }
    }
    if x-1 >= 0 { // up
        if _exist(x-1, y, word[1:], board) {
            return true
        }
    }

    board[x][y] = word[0]

    return false
}
```

因為 Go 不像 Python 直接可以用 inner function，所以上面把 `_exist()` 搬出去

如果不搬，那就是變成要用 go 的 function variable 概念來做

``` go
func exist(board [][]byte, word string) bool {
    h := len(board)
    w := len(board[0])

    var _exist func(x int, y int, word string) bool // 要先宣告，這樣之後在 _exist() 裡面才有辦法呼叫到 _exist()

    _exist = func(x int, y int, word string) bool {
        if len(word) == 0 {
            return true
        }

        if board[x][y] != word[0] {
            return false
        } else if len(word) == 1 {
            return true
        }

        board[x][y] = ' '

        if y+1 < w { // right
            if _exist(x, y+1, word[1:]) {
                return true
            }
        }
        if x+1 < h { // down
            if _exist(x+1, y, word[1:]) {
                return true
            }
        }
        if y-1 >= 0 { // left
            if _exist(x, y-1, word[1:]) {
                return true
            }
        }
        if x-1 >= 0 { // up
            if _exist(x-1, y, word[1:]) {
                return true
            }
        }

        board[x][y] = word[0]

        return false
    }

    for i := 0; i < h; i++ {
        for j := 0; j < w; j++ {
            if _exist(i, j, word) {
                return true
            }
        }
    }

    return false
}
```

## Source Code (Rust)

``` rust
impl Solution {
    pub fn exist(mut board: Vec<Vec<char>>, word: String) -> bool {
        let mut words: Vec<char> = word.chars().collect();
        for i in (0..board.len()) {
            for j in (0..board[0].len()) {
                if Solution::_exist(i as usize, j as usize, &words, 0, &mut board) {
                    return true;
                }
            }
        }

        return false;
    }
    
    fn _exist(x: usize, y: usize, words: &Vec<char>, index: usize, mut board: &mut Vec<Vec<char>>) -> bool {
        if words.len() == index {
            return true;
        }

        if board[x][y] != words[index] {
            return false;
        } else if words.len() == index+1 {
            return true;
        }

        let c = board[x][y];
        board[x][y] = ' ';

        if y+1 < board[0].len() { // right
            if Solution::_exist(x, y+1, words, index+1, &mut board) {
                return true;
            }
        }
        if x+1 < board.len() { // down
            if Solution::_exist(x+1, y, words, index+1, &mut board) {
                return true;
            }
        }
        if y != 0 { // left
            if Solution::_exist(x, y-1, words, index+1, &mut board) {
                return true;
            }
        }
        if x != 0 { // up
            if Solution::_exist(x-1, y, words, index+1, &mut board) {
                return true;
            }
        }

        board[x][y] = c;

        return false;
    }
}
```

## Source Code (C)

``` c
bool _exist(int x, int y, char* word, char** board, int* boardSize, int* boardColSize) {
    if (strlen(word) == 0) {
        return true;
    }
    
    if (board[x][y] != word[0]) {
        return false;
    } else if (strlen(word) == 1) {
        return true;
    }

    board[x][y] = ' ';

    if (y+1 < *boardColSize) { // right
        if (_exist(x, y+1, word+1, board, boardSize, boardColSize)) {
            return true;
        }
    }
    if (x+1 < *boardSize) { // down
        if (_exist(x+1, y, word+1, board, boardSize, boardColSize)) {
            return true;
        }
    }
    if (y != 0) { // left
        if (_exist(x, y-1, word+1, board, boardSize, boardColSize)) {
            return true;
        }
    }
    if (x != 0) { // up
        if (_exist(x-1, y, word+1, board, boardSize, boardColSize)) {
            return true;
        }
    }

    board[x][y] = word[0];

    return false;
}

bool exist(char** board, int boardSize, int* boardColSize, char* word){
    for (int i = 0; i < boardSize; i++) {
        for (int j = 0; j < *boardColSize; j++) {
            if (_exist(i, j, word, board, &boardSize, boardColSize)) {
                return true;
            }
        }
    }

    return false;
}
```

<br/>

> 最後也來看一下 4 種語言的 performance
>
> | Runtime | Memory | Language |
> |-|-|-|
> | 4456 ms | 14.3 MB | python3 |
> | 72 ms | 2.1 MB | golang |
> | 64 ms | 2.2 MB | rust |
> | 128 ms | 5.5 MB | c |
