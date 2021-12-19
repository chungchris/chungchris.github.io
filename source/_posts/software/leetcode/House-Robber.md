---
title:
  - '[LeetCode] #198 House Robber'
categories: software/leetcode
hide: true
summary: 給一個整數陣列，找最大可能的不相鄰數總合
date: 2020-09-18 08:06:25
---

## Problem

給一個整數陣列，找最大可能的不相鄰數總合

* [Link](https://leetcode.com/problems/house-robber/)
* 等級：**Medium**
* 推薦指數：[:star::star::star::star:] 典型 DP 題，另外有關鍵是識別出可將 DP 的 function call 簡化為 iteration。DP 問題只的就是每一的解會跟其他輪有關係，若後面輪次的解只跟其前面輪次有關係，那自然就可以用 iteration 即可毋需使用到 recursion

> :star: 有人推薦過的題目的我才會紀錄，所以即使我覺得只有一顆星他依舊是一題有其他人推薦的題目，只是我自己不覺得需要刷
> :star::star: 代表我覺得有時間再看就好
> :star::star::star: 代表可以刷
> :star::star::star::star: 代表一定刷
> :star::star::star::star::star: 代表多刷幾次甚至把所有 solution 都弄懂

## Solution

DP 的想法其實蠻直覺的，很容易識別出來
比如給定一個數列 `[A, B, C, D]`
他的解就會是下列兩者中的較大者
  - 『取 A』 加上 以 `[C, D]` 為題的解
  - （不取 A）以 `[B, C, D]` 為題的解

如果我們將本題的求解函式定義為 `f`，參數為 `nums` 的 offset，代表求以 `nums[offset:]` 為題的解
那就可以寫成 `f(0) = max( nums[0] + f(2), f(1) )`
進一步 generalize 就變成 `f(i) = max( nums[i] + f(i+2), f(i+1) )`
所以就變成了一個 recursive，且其收斂狀況為 `i > len(nums)` 的時候

``` python
class Solution:
    def rob(self, nums: List[int]) -> int:
        d = {}
        n = len(nums)
        
        def _rob(offset):
            nonlocal n
            if offset >= n:
                return 0
            
            if offset in d:
                return d[offset]
            
            maxi = max(_rob(offset+1), nums[offset] + _rob(offset+2))
            d[offset] = maxi
            
            return maxi
            
        return _rob(0)
```

但我們進一步觀察，`f(i)` 跟 `f(i+2)` 和 `f(i+1)` 有關係，整個 recursive 跑起來，整體時間順序上，其實是先跑出 `f(n)` 的結果，然後跑出 `f(n-1)`，然後 `f(n-2)`，如此直到得到 `f(0)`
換個角度，如果我們能事先直接算出 `f(n)` 和 `f(n-1)`，那我們就可以透過 iteration 的方式一路把直到 `f(0)` 得出來
而我們其實知道 `f(n) = 0`，`f(n-1) = nums[n-1]`
由 `f(i) = max( nums[i] + f(i+2), f(i+1) )`，知道 `f(n-2) = max( nums[n-2] + f(n), f(n-1) )`，也就是 `f(n-2) = max( nums[n-2], nums[n-1] )`
以此類推可以得到 `f(n-3)`、`f(n-4)` ... 直到 `f(0)`

舉例來說，如果 `nums` 是 `[A, B, C, D]`，我們是先得到了 `[]` 的解，然後 `[D]` 的解，然候 `[C, D]` 的解，以此類推，最後得以得出 `[A, B, C, D]` 的解

我們已經成功轉化成一個 iteration 而不是 recursive function call 了，但我們再做一個小變化讓 code 更簡潔一點
上面是從 nums 的尾部 iterate 回來，也就是從 n, n-1, n-2 直到 0
但本題其實從頭跑和從尾巴跑並無不同，也就是你要從街頭的第一家開始考慮怎麼搶，或者從街尾那家開始考慮怎麼搶，並無不同
也就是說，如果 `nums` 是 `[A, B, C, D]`，我們是先得到了 `[]` 的解，然後 `[A]` 的解，然候 `[A, B]` 的解，以此類推，最後也得以得出 `[A, B, C, D]` 的解，與上面的解並不會不同
他還是呈現一樣的規律：本輪的解，會是下列兩者取其大者
  - 本輪的數字 + 上上輪的解
  - 上一輪的解

也就是一樣 `g(x) = max( nums[x] + g(x-2), g(x-1) )`
然後若定義 `g(0) = nums[0]`, `g(-1) = 0`, 那我們要求的就是 `g(len(nums)-1)`
所以就可以寫成

``` python
class Solution:
    def rob(self, nums: List[int]) -> int:
        prev = 0
        curr = nums[0]
        for x in range(1, len(nums)):
            prev, curr = curr, max(nums[x] + prev, curr)
        return curr
```

可以再稍微簡化成

``` python
class Solution:
    def rob(self, nums: List[int]) -> int:
        prev = 0
        curr = 0
        for n in nums:
            prev, curr = curr, max(n + prev, curr)
        return curr
```

所以其實繞了一圈，我們回頭來看
從尾擴展：`f(i) = max( nums[i] + f(i+2), f(i+1) )`, `f(n) = 0`, `f(n-1) = nums[n-1]`
從頭擴展：`g(x) = max( nums[x] + g(x-2), g(x-1) )`, `g(0) = nums[0]`, `g(-1) = 0`
從尾擴展的想法會比較容易先走向 recursive function 作法，因為『本輪的解，會取決於下兩輪的解』
從頭擴展的想法會比較容易先走向 iteration 作法，因為『本輪的解，會取決於前兩輪的解』
