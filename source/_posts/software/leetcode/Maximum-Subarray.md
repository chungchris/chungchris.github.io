---
title:
  - '[LeetCode] #53 Maximum Subarray'
categories: software/leetcode
hide: true
summary: 找和為最大的子陣列
date: 2020-07-19 10:13:02
---

## Problem

找和為最大的子陣列，子陣列至少包含一個數，

* [Link](https://leetcode.com/problems/maximum-subarray/)
* 等級：**Easy**
* 推薦指數：[:star::star::star:] 我覺得這題可能也可以 Medium。會被歸在 Easy 大概是因為 `O(n)` 解就是很典型的這類 array 題 greedy 的思路，他是一題經典的基本 DP (Dynamic Programming) 題，也就是存在 `f(i) = g(f(i-1))` 這樣的特性，本題的 `g` 就是 `max(f(i-1)+nums[i], nums[i])`。另外有 `O(n log n)` 的 divide and conquer 解可能是直觀一點的解，是一個典型的 divide and conquer 範例，所以可能也是另一個歸在 Easy 的原因吧

> :star: 有人推薦過的題目的我才會紀錄，所以即使我覺得只有一顆星他依舊是一題有其他人推薦的題目，只是我自己不覺得需要刷
> :star::star: 代表我覺得有時間再看就好
> :star::star::star: 代表可以刷
> :star::star::star::star: 代表一定刷
> :star::star::star::star::star: 代表多刷幾次甚至把所有 solution 都弄懂

## O(n) 解法

假設 input 為

|       |   |   |   |   |   |   |   |   |   |
|-------|---|---|---|---|---|---|---|---|---|
| index | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
| value |-2 | 1 |-3 | 4 |-1 | 2 | 1 |-6 | 4 |

<br/>

我們從左往右看

|       |   |
|-------|---|
| index | 0 |
| value |-2 |

第一輪，只看到 index 0，解就是 -2 沒問題

<br/>

|       |   |   |
|-------|---|---|
| index | 0 | 1 |
| value |-2 | 1 |

第二輪，看到 index 1，解就變成是 1
1 怎麼來的？是從 3 種可能中取最大者：第一種可能是 `-2`，第二種是 `(-2 + 1)`，第三種可能是 `1`，所以解是 `1`
但要注意，其實新增的可能解只有兩種，即 `(-2 + 1)` 和 `1`，因為 `-2` 是上一輪就已經知道的可能解

<br/>

|       |   |   |   |
|-------|---|---|---|
| index | 0 | 1 | 2 |
| value |-2 | 1 |-3 |

第三輪，看到 index 2
我們能夠取得的新的可能解，一樣是會新增兩種，一種就是 index 2 本身，一種是 index 2 加上 index 1 以前的不定長度子陣列所產生的解
第一種沒問題，就是 `-3`
第二種，index 2 加上 index 1 以前的不定長度子陣列
1. 我們其實不關心到底子陣列是從哪裡到哪裡，因為這題只需要找和的最大值
2. 『index 2 加上 index 1 以前的不定長度子陣列』這勢必包含 index 1
3. 我們在上一輪已經得到，含有 index 1 的可能和，就是 `(-2 + 1)` 和 `1`，而其中又以 `1` 是較大者，所以會得到新增可能解為 `1 + (-3)`（`(-2 + 1) + (-3)` 可以不用管他，因為他一定比較小）
綜合起來，本輪我們會得到新增 2 個可能，`-3`、`1 + (-3)`，而兩者中又以 `1 + (-3) = -2` 較優

到此我們可以歸納出來：
每一輪都會新增兩個可能解，來自於 `該輪新看到的 index 本身的值`，以及 `該輪新看到的 index 本身的值 + 前一輪新增的解中的較佳者`

<br/>

我們再多看一輪

|       |   |   |   |   |
|-------|---|---|---|---|
| index | 0 | 1 | 2 | 3 |
| value |-2 | 1 |-3 | 4 |

這一輪會新增的可能解：
第一種來自 `該輪新看到的 index 本身的值`，也就是 `4`
第二種來自 `前一輪新增的解中的較佳者`，也就是 `1 + (-3) = -2`，加上 `該輪新看到的 index 本身的值`，也就是 -2 + 4 = `2`

<br/>

我們新增一個 row 把後者都顯示出來，也再新增一個 row 把每一輪最佳解顯示出來（也就是取上面兩個 row 中較大者）

|                                               |   |   |   |   |   |   |   |   |   |
|-----------------------------------------------|---|---|---|---|---|---|---|---|---|
| index                                         | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
| value                                         |-2 | 1 |-3 | 4 |-1 | 2 | 1 |-6 | 4 |
|該輪新看到的 index 本身的值 + 前一輪新增的解中的較佳者|   |-1 |-2 | 2 | 3 | 5 | 6 | 0 | 4 | 
|包含該輪新看到的 index 的較佳解                    |-2 | 1 |-2 | 4 | 3 | 5 | 6 | 0 | 4 | 

最後一個 row 當中的最大值就是本題的最終解，也就是 `6`

<br/>

把想法轉成 code 如下
其中包含一個簡化：如果我們發現前一輪的較佳解是負的，那本輪的較佳解就是自己本身，不用加之前的了，因為加了只會更小

``` python
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        ans = nums[0]
        prev = nums[0] # the better solution of previous round
        for i in range(1, len(nums)):
            prev = nums[i] if prev < 0 else prev + nums[i]
            ans = prev if prev > ans else ans
        return ans
```

以上就是一個 `O(n)` 的解

## O(nlogn) 解法

但題目又有另外說看有沒有 divide and conquer approach
不過這題用 divide and conquer 其實時間複雜度是比較差的，而且還因此用到 recursive。可能因為演算法課本在講 divide and conquer approach 時有用這個例子(?)，所以可能某方面來說 divide and conquer approach 會是更直覺的想法

divide and conquer 就是顧名思義，先把大問題 devide 成小問題，分別求小問題的解，在合成出大問題的解

假設一個陣列 `a[0:n]` 的本題最佳解我們表示為 `f(a, 0, n)`
我們取 0, n 的中間值假設為 `mid`
那本題最佳解 `f(a, 0, n)` 就存在於 3 種可能性的其中一種
1. 最佳解的那個 subarray 的存在於 `a[0:mid]` 之間，也就是存在 `mid` 的左半邊，不包含 `mid`，可以表示成 `f(a, 0, mid)`
2. 最佳解的那個 subarray 的存在於 `a[mid+1:n]` 之間，也就是存在 `mid` 的右半邊，不包含 `mid`，可以表示成 `f(a, mid+1, n)`
3. 最佳解的那個 subarray 有包含到 `mid`，也就是從 `mid` 往左右延伸，取得最大的可能和
本題最佳解 `f(a, 0, n)` 就是上述 3 種可能中最大的那一個

所以就可以看出他是一個 recursive 問題，每次取中間 n 就會被對半切，所以有 log n 組遞迴
而每一次的 f 當中，主要的 iteration 會在 3 的部分，他要由 mid 往左右延伸，其期待值把同一層的 recursive 綜合起來看雖然可能不到 n 還是 n 的相關數
所以乘起來就是 `O(nlogn)`

既然時間複雜度比較差那我就不寫了...
