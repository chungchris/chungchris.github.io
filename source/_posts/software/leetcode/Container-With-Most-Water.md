---
title:
  - '[LeetCode] #11 Container With Most Water'
categories: software/leetcode
hide: true
summary: 選兩個柱子使可以裝最多的水
date: 2020-07-08 16:39:23
img:
top:
cover:
coverImg:
author:
password:
toc:
mathjax:
reprintPolicy:
---

## Problem

![選兩個柱子使可以裝最多的水](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/07/17/question_11.jpg)

* [Link](https://leetcode.com/problems/container-with-most-water/)
* 等級：**Medium**
* 推薦指數：[:star::star::star:] 暴力解很容易，進階解不一定想到到。但只要先以左右兩側為第一個選擇，想辦法去提升容量，演算法就很容易出來 

> :star: 有人推薦過的題目的我才會紀錄，所以即使我覺得只有一顆星他依舊是一題有其他人推薦的題目，只是我自己不覺得需要刷
> :star::star: 代表我覺得有時間再看就好
> :star::star::star: 代表可以刷
> :star::star::star::star: 代表一定刷
> :star::star::star::star::star: 代表多刷幾次甚至把所有 solution 都弄懂

---

## 暴力解 也是最直觀的解法

### 想法

選兩條柱子，就全部可能的 pair 都拿來算一下面積，取最大的，時間複雜度自然是 `O(n^2)`

### Source Code (Python)

``` python
class Solution:
    def maxArea(self, height: List[int]) -> int:
        l = len(height)
        ans = 0
        for i in range(0, l):
            for j in range(i+1, l):
                t = (j-i) * min(height[i], height[j])
                ans = t if t > ans else ans
        return ans
```

---

## 進階解

### 想法

我們已經有 `O(n^2)` 的解法，那要進階，代表不能有*挑兩次*的動作，要想辦法提升我們的挑選策略

最大面積由柱子的水平距離和高度較低者構成，所以挑選自然是從挑高的，或挑遠的兩個方向著手
從高度來看：除非我們做過 sort，不然我們也不知道出起手式怎樣挑是最佳解。即使我們做過 sort，就有了 `O(n logn)` 的基本包袱，不是不可能，但我們先放著
從距離來看：我們希望兩根柱子水平距離越遠越好，所以最好是最左和最右就是最佳解，我們可以從這個選擇出發，這是本題的第一個癥結點

然後，當我們選了最左和最右出發，接下來我們會想嘗試把選擇往中間移動
但要選擇先把左側的選擇往內選一個呢？還是先把右側的選擇往內選一個？這裏就必須意識到，也是本題的第二個癥結點，進一步移動原本的選擇當中較高的那一邊其實對事情沒有幫助，因為水槽的高度已經被較低的那一邊給限制住，如果改變原本的選擇當中較高的那一邊，只是讓水池水平距離往內縮，水池高度不變，面積只會縮小
所以我們下一步的選擇是針對比較低的那一邊，換成選往中間靠的下一個，期待取得更大的面積；當然，很簡單可以察覺到，選擇到比原本高的才有意義，如果比原本低那面積只會縮小。當選到一個比原本高的，這時候就需要乘乘看才知道是不是比原本面積大了了，因為水平距離已經內縮了一到數單位

### Source Code (Python)

``` python
class Solution:
    '''
    The vloume of water depends on container width and height
        width: choose the left bar and the right bar will be the best case
        height: the shorter one will dominates the vloume
    
    There are 2 direction to go: choose bars based on width first or based on height first.
        height: have to sort the array. it takes at least O(n log n) time
        width: just choose the left and right ends first
    
    So we can do it with width first. We choose the left and right ends first. Can we have better solution when we move inward the bars?
        * Move the shorter one will help. Move the taller one won't because the overall height is still limited by the shorter one.
        * Move the shoter one. If next one is even shorter, it won't help. Better keep moving to find a taller one.
    
    Test cases:
      normal: [4,3,2,1,4]
      special: [4,2,100,99,2,5], [4,2,99,100,2,5]
    '''
    def maxArea(self, height: List[int]) -> int:    
        ans = 0
        i = 0
        j = len(height) - 1
        while i < j:
            area = (j-i) * min(height[i], height[j])
            ans = area if area > ans else ans
            if height[i] < height[j]:
                t = height[i]
                while height[i] <= t:
                    i = i + 1
                    if i >= j:
                        return ans
            else:
                t = height[j]
                while height[j] <= t:
                    j = j - 1
                    if i >= j:
                        return ans
        return ans
```

可以觀察到這個解法只需要 `O(n)`，已經優於 `O(n logn)` 所以我們不用再去想先挑高度出發的方法
