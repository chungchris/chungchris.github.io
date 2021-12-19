---
title:
  - '[LeetCode] #15 3sum'
categories: software/leetcode
hide: true
summary: 取三數總和為 0
date: 2020-07-09 08:10:50
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

取三數總和為 0

* [Link](https://leetcode.com/problems/3sum/)
* 等級：**Medium**
* 推薦指數：[:star::star::star:] 暴力解就是 `O(n^3)`，進階解不一定想到到。想有比 `O(n^3)` 快的方法，掃一次是一定需要的，所以至少有 n，重點在於掃一次取得第一個數後，怎麼挑另外兩個數，並且不能是 `O(n^2)`，所以合理可以想到嘗試先 sort 過，那接下來就會變得跟 [#11 Container With Most Water](https://chungchris.github.io/2020/07/08/software/leecode/Container-With-Most-Water/) 很像。但另外還有檢查重複的部分也需要一些琢磨

> :star: 有人推薦過的題目的我才會紀錄，所以即使我覺得只有一顆星他依舊是一題有其他人推薦的題目，只是我自己不覺得需要刷
> :star::star: 代表我覺得有時間再看就好
> :star::star::star: 代表可以刷
> :star::star::star::star: 代表一定刷
> :star::star::star::star::star: 代表多刷幾次甚至把所有 solution 都弄懂

## 想法

挑 3 個數，自然知道暴力解就是 `O(n^3)`，所以有想想看有沒有比 `O(n^3)` 快的方法，掃一次是一定需要的，所以至少有 n，重點在於掃一次取得第一個數後，怎麼挑另外兩個數

> 這題其實跟 [Container With Most Water](https://chungchris.github.io/2020/07/08/software/leecode/Container-With-Most-Water/) 相似，都是從兩個方向往結果更好的選擇逼近

這時候出現第一個癥結點，在挑選另外兩個數的時候，如果我們要產生 `O(n)` 的策略，那一定要 sort 過，不然挑選上根本無從憑據

|     |   |   |   |   |   |   |   |   |
|-----|---|---|---|---|---|---|---|---|
|index| 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 |
|value|-7 |-5 |-2 |-1 | 0 | 3 | 4 | 5 |

1. 對於一個已經 sort 過的數列，我們今天要挑出 a, b, c 3 個數
2. 挑 a 時無從下策略，所以就遍歷
3. 挑 b,c 時，目標是要讓他們跟 a 的和為 0，我們試著先挑最大和最小，這算是一種蠻直覺的策略。例如，當目前 a = n[0] = -7，b 挑最小就是 a 的右邊一個 b = n[1] = -5，c 挑最大就是尾端 c = n[-1] = n[7] = 5
4. 此時 a + b + c = (-7) + (-5) + (5) = -7，距離我的目標 0，我們要嘗試讓 b, c 變成更大的值才行，因為我們在上一步挑的 c 已經是最大，所以現在唯一該替換的就是 b，若 b 的挑選往右跳，他就會越來越大

時間複雜度就是第 1 步的 n，疊上 3,4 步的 n 所以是 `O(n^2)`，如果 sort 的部分算 `O(n logn)`，那目前就是 `n logn + n^2 `

另外還有一個問題是重複性，如果檢查重複用暴力解，那就是比如有 x 個解，要比 3 個數，那就是 3x，因為 x 也是 n 的相關數，所以還是 `O(n)` 所以對於時間複雜度的影響不大。但實際上執行起來還是可以藉由一個想法省些時間，關鍵就在於我們的數列已經排序過，所以如果找到一個解，只要該解跟上一個找到的解沒有重複，那他就是一個重複的解

1. 假設在某一輪遍歷選擇到某 a0
2. 在該輪 b,c 的選擇經過上述規則不斷遞移，找到 b0,c0 使 a0 + b0 + c0 = 0
3. b,c 的選擇繼續走，找找看還有沒有其他的 b,c 能和 a0 和為 0。假設又找到 b1,c1 達成 a0 + b1 + c1 = 0
4. 根據我們選擇 b,c 的邏輯，我們知道一定 b1 >= b0，因為選 b 的時候我們是不斷往右移；一定 c1 <= c0，因為選 c 的時候我們是不斷往左移。所以如果 [b1, c1] != [b0, c0]，那就算還另外存在 b2,c2 使得 a0 + b2 + c2 = 0，[b2, c2] 也不可能等於 [b0, c0]
5. 當 a 進到下一輪，如果與前一個 a 值相同，那可以直接跳過，因為於他右側的數列中選擇出的 b 和 c，一定會跟前一輪選出來的相同，甚至更少

---

## Source Code (Python)

``` python
class Solution:
    '''
    Pick 3 nums. The brute force solution needs O(n^3). Let try to find is there better solution.

    How to pick the first num, say `a`? There is no strategy. Just pick one. Very likely need to traverse all of them.
    How to pick the remaining 2 nums to let a+b+c=0. There will be still no strategy is the nums is not in some kind of order.

    We will have to sort the list first.

    For example, after sort, [-4,-1,-1,0,1,2]
    Let a=-4
        How to choose b and c? We can have a strategy now. Let b is the smallest one and c is the largest one. i.e. b=-1, c=2
        sum = -4 + -1 + 2 = -3, it's too small.
        We shall change b. It the possibility to make sum larger, instead of changing c.
    
    Test cases:
        [-1,0,1,2,-1,-4]
        [0, 0, 0, 0, 0]

    '''
    def threeSum(self, nums: List[int]) -> List[List[int]]:
        nums.sort()

        ans = []
        l = len(nums)
        for i in range(l-2):
            if i >= 1 and nums[i] == nums[i-1]:
                continue

            x = i+1
            y = l-1
            ans_temp = []
            while x<y:
                s = nums[i] + nums[x] + nums[y]
                if s == 0:
                    if not (len(ans_temp) > 0 and nums[x] == ans_temp[-1][1] and nums[y] == ans_temp[-1][2]):
                        ans_temp = ans_temp + [[nums[i], nums[x], nums[y]]]
                    y = y - 1
                    x = x + 1
                elif s > 0:
                    y = y - 1
                else:
                    x = x + 1

            if len(ans_temp) > 0:
                ans = ans + ans_temp

        return ans
```
