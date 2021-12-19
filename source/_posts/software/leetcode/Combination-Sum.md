---
title:
  - '[LeetCode] #39 Combination Sum'
categories: software/leetcode
hide: true
summary: 給定一些整數，找和為 t 的所有可能
date: 2020-07-12 15:54:12
---

## Problem

給定一些整數，找和為 t 的所有可能，數字可以被重複使用

* [Link](https://leetcode.com/problems/combination-sum/)
* 等級：**Medium**
* 推薦指數：[:star::star::star:] 只要注意到數字可以被重複使用，那這題蠻直觀的其實

> :star: 有人推薦過的題目的我才會紀錄，所以即使我覺得只有一顆星他依舊是一題有其他人推薦的題目，只是我自己不覺得需要刷
> :star::star: 代表我覺得有時間再看就好
> :star::star::star: 代表可以刷
> :star::star::star::star: 代表一定刷
> :star::star::star::star::star: 代表多刷幾次甚至把所有 solution 都弄懂

## 想法

只要注意到**數字可以被重複使用**，那這題蠻直觀的其實
就是給定一群整數當作你可以使用的 candidate，用這些數去加出目標 t
當開始實作就知道，加法問題會變減法，因為當我們採納了第一個數 n，自然會問那現在離目標 t 還有多遠，就是 t-n

假設今天有一群數字 `candidates[0 ,1, 2, ...]`
如果存在解 `t` = `candidates[0]` + `t'`，那 `t'` = `t` - `candidates[0]`，並且 `t'` 也能由 `candidates[0 ,1, 2, ...]` 組出來

所以假設今天有 function `combinationSum(candidates, t)` 會找出所有用 candidates 組成 t 的組合
那 `combinationSum(candidates, t)` = \[
        ...,
        \[ `candidates[i]`, iterate `combinationSum(candidates, t-candidates[i])` \],
        ...,
    \]

如此命題就就可以變 recursive
另外只要再注意一下不要產生重複的組合就行

## Source Code (Python)

``` python
class Solution:
    def combinationSum(self, candidates: List[int], target: int) -> List[List[int]]:        
        ans = []
        for i in range(len(candidates)):
            n = candidates[i]
            if target == n:
                ans.append([n])
            elif target > n:
                r = self.combinationSum(candidates[i:], target-n) # candidates[i:] 這就是用來避去使用已經處理過的數字
                for c in r:
                    ans.append([n] + c)
        return ans
```
