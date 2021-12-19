---
title:
  - '[LeetCode] #56 Merge Intervals'
categories: software/leetcode
hide: true
summary: Merge Intervals
date: 2020-07-21 09:48:21
---

## Problem

* [Link](https://leetcode.com/problems/merge-intervals/)
* 等級：**Medium**
* 推薦指數：[:star::star::star:] 想出解法容易，但可能會瑣碎，不一定能直接想到最簡潔的解法，讓其變簡潔的關鍵就是要先把 intervals 據其起點做 sort

> :star: 有人推薦過的題目的我才會紀錄，所以即使我覺得只有一顆星他依舊是一題有其他人推薦的題目，只是我自己不覺得需要刷
> :star::star: 代表我覺得有時間再看就好
> :star::star::star: 代表可以刷
> :star::star::star::star: 代表一定刷
> :star::star::star::star::star: 代表多刷幾次甚至把所有 solution 都弄懂

## 想法

因為大家可能都做過 [#20 Valid Parentheses](https://chungchris.github.io/2020/07/10/software/leetcode/Valid-Parentheses/)，所以容易淪入相同的思路
但差別在 Valid Parentheses 只關注能不能全部配對成功，他不關心區間。而且另一個更大的差別是，Valid Parentheses 不會有括號重疊在同一個位置的狀況
用同樣的思路去想解法一樣會解的出來，但因為 case 很多，可能遇到測資 fail 就加 condition 修，那 code 就會變得很瑣碎

可以換成用另外一個角度出發，我們先把 `intervals` 據其起點做 sort，這樣可以確保至少我們再 iterate 的時候，interval left bound 是遞增的
然後我們維護一個我的解 `ans`，在每一次看到一個新的 interval 時去更新 `ans`
因為我們已經確認了 left bound 是遞增關係，所以就可以讓 condition 變得簡潔許多

## Source Code (Python)

``` python
'''
(  )
  (   )
  (     )
    (     )
             (  )
'''
class Solution:
    def merge(self, intervals: List[List[int]]) -> List[List[int]]:
        intervals.sort(key = lambda x: x[0])
        ans = []
        for i in intervals:
            if not ans:
                ans.append(i)
            elif i[0] > ans[-1][1]: # 注意本題比如 [1,3], [4,5] 不會連成 [1,5]
                ans.append(i) # 因為新的 interval 脫離了故有的領域，代表可以開啟下一個獨立的區間
            elif i[1] > ans[-1][1]: # expand 原本的領域
                ans[-1][1] = i[1]
        return ans
```
