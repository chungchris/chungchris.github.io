---
title:
  - '[LeetCode] #300 Longest Increasing Subsequence'
categories: software/leetcode
hide: true
summary: 給定一個數列，取其中若干不連續從小到大排列的數字，求該數列最大可能長度
date: 2020-10-17 14:45:11
---

## Problem

給定一個數列，取其中若干不連續從小到大排列的數字，求該數列最大可能長度

* [Link](https://leetcode.com/problems/longest-increasing-subsequence/)
* 等級：**Medium**
* 推薦指數：[:star::star::star:] 這種 array 類型一慣的思路都是用 greedy，就是從頭走，一步一步多看一個數字，然後去更新最佳解。但即使這題也是相同，不過沒做過還是不容易想到做法

> :star: 有人推薦過的題目的我才會紀錄，所以即使我覺得只有一顆星他依舊是一題有其他人推薦的題目，只是我自己不覺得需要刷
> :star::star: 代表我覺得有時間再看就好
> :star::star::star: 代表可以刷
> :star::star::star::star: 代表一定刷
> :star::star::star::star::star: 代表多刷幾次甚至把所有 solution 都弄懂

## Solution

就是從頭走，一步一步多看一個數字，然後去更新最佳解
我們所維護的最佳解，數字應該儘可能取小的，這樣可以留給未來更多可能性
舉例來說，我們在某階段維護的最佳解是 `[2, 5, 9]`，然後接著新看到 `7`，那我們應該記把他變成 `[2, 5, 7]`，因為 `[2, 5, 9]` 和 `[2, 5, 7]` 其實都是最佳解，但 `[2, 5, 7]` 留給未來更多可能性
繼續假設我們接著看到 `10`，那因為他比 7 大，所以可以直接加入最佳解，變成 `[2, 5, 7, 10]`
再來看一種可能比較複雜的狀況，假設我們接著看到 `6`，這時未來最終的最佳解有兩種走向，一種是 `[2, 5, 6, ...]`，另一種是 `[2, 5, 7, 10, ...]`，我們如何確定哪一種才是最佳解？
這裡就是我覺得這個解法 tricky 的地方：我們可以將維護的最佳解變成 `[2, 5, 6, 10]`，這個作法可以同時 cover 這兩種走向
把 7 換成 6，是承襲剛剛『留給未來更多可能性』的相法，因為本題只關心長度，所以把 `[2, 5, 7, 10]` 換成 `[2, 5, 6, 10]` 並不會對長度有影響；另一方面，把 10 留住，又可以 cover 最佳解為 `[2, 5, 7, 10, ...]` 的可能性

``` python
class Solution(object):
    def lengthOfLIS(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        cache = [nums[0]]
        for n in nums[1:]:
            if n > cache[-1]:
                cache.append(n)
            else:
                index = bisect_left(cache, n)
                cache[index] = n
        return len(cache)
```
