---
title:
  - '[LeetCode] #55 Jump Game'
tags:
  - leecode:sum
categories: software/leetcode
hide: true
summary: 能否跳到終點
date: 2020-07-20 14:14:05
---

## Problem

取三數總和為 0

* [Link](https://leetcode.com/problems/jump-game/)
* 等級：**Medium**
* 推薦指數：[:star::star::star::star:] 培養認出適合用 Greedy 演算法的問題類型

> :star: 有人推薦過的題目的我才會紀錄，所以即使我覺得只有一顆星他依舊是一題有其他人推薦的題目，只是我自己不覺得需要刷
> :star::star: 代表我覺得有時間再看就好
> :star::star::star: 代表可以刷
> :star::star::star::star: 代表一定刷
> :star::star::star::star::star: 代表多刷幾次甚至把所有 solution 都弄懂

## DP 解法

DP (dynamic programming) 和 Greedy 都是算處理本題蠻直觀的想法，但先想到哪一個就有可能導致造盲區而想不到另一個
以本題來說，Greedy 演算法是比 DP 更快，所以如果先想到 DP，就有可能反而想不到 Greedy

DP 的想法是
如果給定一個陣列 `a[0:n]`，存在解 `f(a, 0) = True` 代表從 `a` 的 index `0` 出發有辦法到達 `a` 的 index `n-1`
那有兩種可能
* 第一種：可以從 0 直接跳到終點，也就是 `0 + a[0] >= n - 1`
* 第二種：至少存在一個點 index `i`，`0 < i < n`，使得 `f(a, i) = True`

``` python
class Solution:
    def canJump(self, nums: List[int]) -> bool:
        length = len(nums)
        failed = [ False ] * length
        
        def _canJump(start):
            if start == length-1:
                return True
            if failed[start]:
                return False

            if start + nums[start] >= length - 1:
                return True
            for i in reversed(range(start+1, start+nums[start]+1)):
                if _canJump(i):
                    return True

            failed[start] = True
            return False
        
        if _canJump(0):
            return True
        return False
```

## Greedy 解法

關鍵點是我們不是只能跳每一個 integer 指定的步數，而是小於該 integer 的步數都可以
這造成，當我們有辦法抵達某一個 index `i`，根據該 index 提供的 integer `a[i]`，我們能夠到達更遠的地方也就是 index `i + a[i]`，而且，不是只能到達 index `i + a[i]` 而已，而是 index `i + a[i]` 以前的所有點我們都可以到達
同時，index `i + a[i]` 以前的所有點的 integer，都能夠成為擴展我們的可到達區域的潛在武器。也就是說，我們目前的可到達區域，變成是 `Max( k + a[k] )`，其中 `0 <= k <= i`

如果今天 `Max( k + a[k] )` 可以覆蓋到 index `n-1`，那就代表我們能夠抵達終點

一開始我們的 k 只有 `0`，因為一開始我們能抵達的點只有 index `0`
但藉由 `a[0]` 提供的值，我們就可以儘量大的擴展出去，找到做大的那個可能變成新的 `k`

我們把 `k` 的概念命名為 `reached` 實作如下

``` python
class Solution:
    def canJump(self, nums: List[int]) -> bool:
        length = len(nums)
        reached = 0
        for i in range(length):
            if i <= reached:
                reached = i + nums[i] if i + nums[i] > reached else reached
                if reached >= length-1:
                    return True
            else:
                return False
        return False
```

為什麼 Greedy 比較省時間，因為 Greedy 只關心『是否可能有解』；反過來說，他能夠有效率的判斷出『無解』的狀況，也就是第 11,12 行的作用
如果採用 DP，他需要要把所有有辦法踩到的點都踩完，才能夠下『無解』的結論，看下面這個例子就很明顯

|       |   |   |   |   |   |   |   |   |   |
|-------|---|---|---|---|---|---|---|---|---|
| index | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
| value | 7 | 6 | 5 | 4 | 3 | 2 | 1 | 0 | x |

如果採用 Greedy，for loop 跑完 9 輪就知道無解，`reached` 一直無法突破 7 到達 8

但如果用 DP，總共嘗試的路徑用 index 表示就會是
    0 -> 7 return False, failed[7]=True
    0 -> 6 -> 7 return False, failed[6]=True
    0 -> 6 return False,
    0 -> 5 -> 7 return False
    0 -> 5 -> 6 return False, failed[5]=True
    0 -> 5 return False,
    0 -> 4 -> 7 return False
    0 -> 4 -> 6 return False
    0 -> 4 -> 5 return False, failed[4]=True
    0 -> 4 return False,
    0 -> 3 -> 7 return False
    0 -> 3 -> 6 return False
    0 -> 3 -> 5 return False
    0 -> 3 -> 4 return False, failed[3]=True
    0 -> 3 return False,
    0 -> 2 -> 7 return False
    0 -> 2 -> 6 return False
    0 -> 2 -> 5 return False
    0 -> 2 -> 4 return False
    0 -> 2 -> 3 return False, failed[2]=True
    0 -> 2 return False,
    0 -> 1 -> 7 return False
    0 -> 1 -> 6 return False
    0 -> 1 -> 5 return False
    0 -> 1 -> 4 return False
    0 -> 1 -> 3 return False
    0 -> 1 -> 2 return False
    0 -> 1 -> 6 return False, failed[1]=True
    0 -> 1 return False,
    return False

上面所嘗試的路徑數量已經超過 9 不少
也就是說 DP 解所關心的『路徑』解，在本題用不到，本題只關心能不能到達，所以用 Greedy 比較快
