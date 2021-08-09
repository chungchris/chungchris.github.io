---
title:
  - '[LeetCode] #57 Insert Interval'
tags:
  - leecode:interval
categories: software/leetcode
hide: true
summary: Insert Interval
date: 2020-07-21 11:21:30
---

## Problem

Insert Interval

* [Link](https://leetcode.com/problems/insert-interval/)
* 等級：**Medium**
* 推薦指數：[:star::star::star:] 隨然說是 [#56 Merge Intervals](https://leetcode.com/problems/merge-intervals/) 的延伸，但也不見得就做得出這題

> :star: 有人推薦過的題目的我才會紀錄，所以即使我覺得只有一顆星他依舊是一題有其他人推薦的題目，只是我自己不覺得需要刷
> :star::star: 代表我覺得有時間再看就好
> :star::star::star: 代表可以刷
> :star::star::star::star: 代表一定刷
> :star::star::star::star::star: 代表多刷幾次甚至把所有 solution 都弄懂

## 想法

解法看了就能懂，但我無法找到什麼太明確的思考方法可以引導到這個解法，就當作是一種類型吧...

## Source Code (Python)

``` python
class Solution:
    def insert(self, intervals: List[List[int]], newInterval: List[int]) -> List[List[int]]:
        ans = [] # 跟 #56 一樣，採用『擴展』的作法是第一個重點
        for i in range(len(intervals)):
            if newInterval[1] < intervals[i][0]:
                ans.append(newInterval)
                ans += intervals[i:]
                return ans
            if newInterval[0] > intervals[i][1]:
                ans.append(intervals[i])
            else: # 第二個重點就是這裏去 update newInterval 的值的這種做法
                newInterval[0] = min(newInterval[0], intervals[i][0])
                newInterval[1] = max(newInterval[1], intervals[i][1])
        ans.append(newInterval)
        return ans
```
