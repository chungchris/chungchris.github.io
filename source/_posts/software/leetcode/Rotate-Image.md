---
title:
  - '[LeetCode] #48 Rotate Image'
tags:
  - leetcode:2darray
categories: software/leetcode
summary: 給一個二維方形矩陣，將其旋轉 90 度
hide: true
date: 2020-07-12 20:16:30
---

## Problem

給一個二維方形矩陣，將其旋轉 90 度

* [Link](https://leetcode.com/problems/rotate-image/)
* 等級：**Medium**
* 推薦指數：[:star::star::star:] 兩種解法，一種很容易看到眼花，一種要做過才知道

> :star: 有人推薦過的題目的我才會紀錄，所以即使我覺得只有一顆星他依舊是一題有其他人推薦的題目，只是我自己不覺得需要刷
> :star::star: 代表我覺得有時間再看就好
> :star::star::star: 代表可以刷
> :star::star::star::star: 代表一定刷
> :star::star::star::star::star: 代表多刷幾次甚至把所有 solution 都弄懂

## 解法 A：index 換算

這是最直觀的想法，但寫起來就有些瑣碎

因為是一個四邊形，所以可以觀察到旋轉其實可以分成四組，然後就可以找到他們換算關係
比及假設起點為 `(x,y)`，那下圖四個顏色的區塊就可以一次做搬移

![紅色搬到藍色，藍色搬到綠色，綠色搬到橘色，橘色搬到紅色](https://drive.google.com/uc?export=view&id=14cE4XdtNYFcWL3NDbXjev3T-vzbUyupp)

並且可以找到以 `(x,y)` 和邊長 `len` 的換算關係，寫成示意 code 就如下

``` python
        def _rotate(x, y, length):
            for k in range(0, length-1):
                紅色搬到藍色 matrix[x][y+k] -> matrix[x+k][y+length-1]
            for k in range(0, length-1):
                藍色搬到綠色 matrix[x+k][y+length-1] -> matrix[x+length-1][y+length-1-k]
            for k in range(0, length-1):
                綠色搬到橘色 matrix[x+length-1][y+length-1-k] -> matrix[x+length-1-k][y]
            for k in range(0, length-1):
                橘色搬到紅色 matrix[x+length-1-k][y] -> matrix[x][y+k]
```

於是他們其實可以合併成一個 iteration，並且其實我們的 y 都會等於 x，所以就變成

``` python
        def _rotate(x, length):
            y = x
            for k in range(0, length-1):
                matrix[x+k][y+length-1], \
                    matrix[x+length-1][y+length-1-k], \
                        matrix[x+length-1-k][y], \
                            matrix[x][y+k] = \
                matrix[x][y+k], \
                    matrix[x+k][y+length-1], \
                        matrix[x+length-1][y+length-1-k], \
                            matrix[x+length-1-k][y]
```

這樣我們就做了一個正方形中某一圈的轉換

然後我們可以觀察到，只要把正方形矩陣斜對角上一半的點餵進去 `_rotate()`，矩陣當中所有的點就都會被處理到
比如 5*5 的矩陣：x 餵 0，以 (0,0) 為起點的那圈就會被處理到；x 餵 1，以 (1,1) 為起點的那圈就會被處理到；以此類推

完整的解就會變成

``` python
class Solution:
    def rotate(self, matrix: List[List[int]]) -> None:
        """
        Do not return anything, modify matrix in-place instead.
        """
        def _rotate(x, length):
            for k in range(0, length-1):
                matrix[x+k][x+length-1], \
                matrix[x+length-1][x+length-1-k], \
                matrix[x+length-1-k][x], \
                matrix[x][x+k] = \
                    matrix[x][x+k], \
                    matrix[x+k][x+length-1], \
                    matrix[x+length-1][x+length-1-k], \
                    matrix[x+length-1-k][x]
        
        l = len(matrix)
        for i in range(int(l/2)):
            _rotate(i, l-i*2)
```

時間複雜度是 `O(n^2)`，n 為邊長，因為第 7 和 18 行的是邊長的相關數
如果從 cell 數來看，因為幾乎每個 cell 都會被移動到，所以說是 `O(cells)` 也行。其實正方型矩陣 n^2 本來就等於 cells 數量

## 解法 B：先以斜對角為軸做鏡像，再以垂直中軸做鏡像

as title... 想到這個作法需要一點感覺... 可能會玩魔術方塊的比較有感覺... 實際試個例子就知道這個作法沒有錯

於是我們就開始把想法轉成 code，把一些 index 寫出來就很容易找到該怎麼換算

![要把黃色和藍色互換，會發現其實很簡單是 (x,y) -> (y,x)](https://drive.google.com/uc?export=view&id=1I7hpIh4XdIw6GQYJ8yuRmOpFWbjVuRfn)

![要把綠色和橘色互換，會發現 x 不變，y 變成 len-1-y](https://drive.google.com/uc?export=view&id=1H1-TsXU9sujb0inPVG-9Kb8VPUagxUCA)

``` python
class Solution:
    def rotate(self, matrix: List[List[int]]) -> None:
        """
        Do not return anything, modify matrix in-place instead.
        """
        length = len(matrix)
        for i in range(1, length):
            for j in range(0, i):
                matrix[i][j], matrix[j][i] = matrix[j][i], matrix[i][j]

        for i in range(0, length):
            for j in range(0, int(length/2)):
                matrix[i][j], matrix[i][length-1-j] = matrix[i][length-1-j], matrix[i][j]
```

時間複雜度一樣是 `O(cells)`，因為第 7 和 11 行 for nest 都分別跑過一半的 cell
