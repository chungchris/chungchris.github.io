---
title:
  - '[LeetCode] #5 Longest Palindromic Substring'
categories: software/leetcode
hide: true
date: 2020-07-08 10:13:59
summary: 找一個給定 string 當中，找最長 palindromic(回文) 的 substring
---

## Problem

找一個給定 string 當中，找最長 palindromic(回文) 的 substring

* [Link](https://leetcode.com/problems/longest-palindromic-substring/)
* 等級：**Medium**
* 推薦指數：[:star::star:] 不算難，但可以作為 DP 的題目，或者是進階演算法有興趣的可以藉此研究 [Manacher's algorithm](https://en.wikipedia.org/wiki/Longest_palindromic_substring#Manacher's_algorithm)

> :star: 有人推薦過的題目的我才會紀錄，所以即使我覺得只有一顆星他依舊是一題有其他人推薦的題目，只是我自己不覺得需要刷
> :star::star: 代表我覺得有時間再看就好
> :star::star::star: 代表可以刷
> :star::star::star::star: 代表一定刷
> :star::star::star::star::star: 代表多刷幾次甚至把所有 solution 都弄懂

## 想法

有不同解法，但對我來說最直覺的是一個 palindromic(回文) 的 string 同時代表有**從中間鏡像**的特性，也就是你可以找到一個中間點，往左右輻射，兩邊的字串相同。有了這個想法之後實作並不難，所以僅給 2/5 推薦指數的原因

（這並不是 DP 解法，所以如果對 DP 有興趣的可能這題可以多加一些星星吧）

## Source Code (Python)

所以只要把每個 index 都當成中心點，都去跑跑看左右輻射，最後取結果最長的就行了。唯一要注意的可能就是中心點的選擇可以是一個或兩個，比如 `aba` 中心點就是 `b`，但 `abba` 中心點是 `bb`

``` python
class Solution:
    '''
    For a palindromic string, cut it from the middle, go left and go right shall read identically.
    Choose all possible middle point. See how long each of then can expand. Just note the middle can be single char or in the middle of 2 chars.

    Test cases:
      normal: babad
      normal: babbad
      single char: a
      all the same: aaa
    '''
    def longestPalindrome(self, s: str) -> str:
        slen = len(s)

        def check(x, y=None):
            if y is None:
                y = x+1
                x -= 1

            while x >= 0 and y < slen:
                if s[x] != s[y]:
                    return (x+1, y-1)
                else:
                    x -= 1
                    y += 1

            return (x+1, y-1)

        ans = (0, 0)
        for i in range(0, slen):
            ret = check(i)
            if ret[1] - ret[0] > ans[1] - ans[0]:
                ans = ret
            if i+1 < slen:
                ret = check(i, i+1)
                if ret[1] - ret[0] > ans[1] - ans[0]:
                    ans = ret

        return s[ans[0]:ans[1]+1]
```

第 32 行的 for loop 跑 n 輪，每輪要進 `check()` 兩次；`check()` 裡面跑的 `while` 是 n 的相關數（實際上應該小於 n ）我們就算 ｎ，所以合起來是 `O(n^2)`
