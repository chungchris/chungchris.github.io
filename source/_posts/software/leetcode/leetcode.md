---
title: Leetcode 刷題筆記
date: 2021-07-22 11:02:55
categories: software/leetcode
keywords:
  - leetcode
  - 刷題
summary: 整理 Leetcode 刷過的題做的筆記
img: https://drive.google.com/uc?export=view&id=1XMAkvD_9UQm05DcGldvzqQ5G4PBKaZoz
---

因為我其實不太愛刷題，覺得意義不明... 可能主要因為我的 career path 並非演算法工程師，所以目的性上每個人不太一樣
我去找了別人推薦的清單，想說如果真的要刷就做一些比較有意義的題目，並且依據我自己的刷題目的和偏好星星

* :star: 因為都已經是有人推薦的題目，所以即使我覺得只有一顆星他依舊是一題有人推的題目，只是我自己不覺得需要刷，有可能很簡單。也有可能是我覺得就算會了也意義不大
* :star::star: 代表我覺得有時間再看就好
* :star::star::star: 代表可以刷
* :star::star::star::star: 代表一定刷
* :star::star::star::star::star: 代表多刷幾次甚至把所有 solution 都弄懂

以下是根據別人的推薦 list 來列的
因為我就不太愛想演算法ㄚ，但刷題對我來說有另外一個意義是維持語言的手感，不然有時候久沒打一個語言就會失去感覺

---

## Blind Curated 75

[Link of the Original List](https://leetcode.com/list/xoqag3yj/)

| # | Title | Level | Star | Tag | Comments |
|---|-------|-------|------|-----|----------|
| [1](https://leetcode.com/problems/two-sum/) | Two Sum | Easy | :star: |  | 小暖身而已 |
| [3](https://leetcode.com/problems/longest-substring-without-repeating-characters/) | [Longest Substring Without Repeating Characters](https://chungchris.github.io/2020/07/07/software/leetcode/Longest-Substring-Without-Repeating-Characters/) | Medium | :star::star::star: | `string` |  |
| [5](https://leetcode.com/problems/longest-palindromic-substring/) | [Longest Palindromic Substring](https://chungchris.github.io/2020/07/08/software/leetcode/Longest-Palindromic-Substring/) | Medium | :star::star: | `dp` `string` | 不算難，但可以作為 DP 的題目，或者是進階演算法有興趣的可以藉此研究 Manacher’s algorithm |
| [11](https://leetcode.com/problems/container-with-most-water/) | [Container With Most Water](https://chungchris.github.io/2020/07/08/software/leetcode/Container-With-Most-Water/) | Medium | :star::star::star: | `2pointer` | 暴力解很容易，進階解不一定想到到，但看過就會 |
| [15](https://leetcode.com/problems/3sum/) | [3Sum](https://chungchris.github.io/2020/07/09/software/leetcode/3sum/) | Medium | :star::star::star: | `sum` | 暴力解很容易，進階解不一定想到到，但看過就會 |
| [19](https://leetcode.com/problems/remove-nth-node-from-end-of-list/) | [Remove Nth Node From End of List](https://chungchris.github.io/2020/07/09/software/leetcode/Remove-Nth-Node-From-End-of-List/) | Medium | :star::star::star: | `ll` | 暴力解很容易，進階解不一定想到到，但看過就會。如果對 Rust 有興趣的可以就本題了解一些 Rust 的限制 |
| [20](https://leetcode.com/problems/valid-parentheses/) | [Valid Parentheses](https://chungchris.github.io/2020/07/10/software/leetcode/Valid-Parentheses/) | Easy | :star::star::star::star::star: | `string` | 經典題，很容易，但如果沒有做過還是可能會漏一些東西 |
| [21](https://leetcode.com/problems/merge-two-sorted-lists/) | [Merge Two Sorted Lists](https://chungchris.github.io/2020/07/11/software/leetcode/Merge-Two-Sorted-Lists/) | Easy | :star::star::star::star::star: | `ll` `sort` | 很容易，但 Python 的 List 太強大，用久了可能會忘了在有限制的單向串列狀況下怎麼處理這個問題。同時這也是一題可以觀察 Rust 語言特性的題目 |
| [23](https://leetcode.com/problems/merge-k-sorted-lists/) | [Merge k Sorted Lists](https://chungchris.github.io/2020/07/11/software/leetcode/Merge-k-Sorted-Lists/) | Hard | :star::star::star::star::star: | `ll` `sort` | 其實應該不到 Hard，尤其是如果已經做過 [#21 Merge Two Sorted Lists]。把 merge 2 個轉換成 merge k 個並沒有額外需要什麼高明想法 |
| [33](https://leetcode.com/problems/search-in-rotated-sorted-array/) | [Search in Rotated Sorted Array](https://chungchris.github.io/2020/07/12/software/leetcode/Search-in-Rotated-Sorted-Array/) | Medium | :star::star::star::star: | `sort` `search` | 需要一點觀察，有可能會走錯方向，另外大於小於其實也有點煩 |
| [39](https://leetcode.com/problems/combination-sum/) | [Combination Sum](https://chungchris.github.io/2020/07/12/software/leetcode/Combination-Sum/) | Medium | :star::star::star: | `sum` | 只要注意到數字可以被重複使用，那這題蠻直觀的其實 |
| [48](https://leetcode.com/problems/rotate-image/) | [Rotate Image](https://chungchris.github.io/2020/07/12/software/leetcode/Rotate-Image/) | Medium | :star::star::star: | `2darray` | 兩種解法，一種很容易看到眼花，一種要做過才知道 |
| [49](https://leetcode.com/problems/group-anagrams/) | [Group Anagrams](https://chungchris.github.io/2020/07/13/software/leetcode/Group-Anagrams/) | Medium | :star::star: | `string` | 土法煉鋼，沒什麼高深想法，稍有練習不同語言基本語法的作用 |
| [53](https://leetcode.com/problems/maximum-subarray/) | [Maximum Subarray](https://chungchris.github.io/2020/07/19/software/leetcode/Maximum-Subarray/) | Easy | :star::star::star: | `sum` | 我覺得這題可能也可以 Medium，O(n) 解很可能要做過才會，會被歸在 Easy 大概是因為他是一題經典的基本 DP (Dynamic Programming) 題吧，也就是存在 f(i) = g(f(i-1)) 這樣的特性。另外有 O(n log n) 的 divide and conquer 解可能是直觀一點的解，也是一個典型的 divide and conquer 範例，所以可能也是另一個歸在 Easy 的原因吧 |
| [54](https://leetcode.com/problems/spiral-matrix/) | Spiral Matrix | Medium | :star: | `2darray` | 就分四組細心處理完就好，沒什麼 |
| [55](https://leetcode.com/problems/jump-game/) | [Jump Game](https://chungchris.github.io/2020/07/20/software/leetcode/Jump-Game/) | Medium | :star::star::star::star: | `sum` | 培養認出適合用 Greedy 演算法的問題類型 |
| [56](https://leetcode.com/problems/merge-intervals/) | [Merge Intervals](https://chungchris.github.io/2020/07/21/software/leetcode/Merge-Intervals/) | Medium | :star::star::star: | `interval` | 想出解法容易，但可能會瑣碎，不一定能直接想到最簡潔的解法 |
| [57](https://leetcode.com/problems/insert-interval/) | [Insert Interval](https://chungchris.github.io/2020/07/21/software/leetcode/Insert-Interval/) | Medium | :star::star::star: | `interval` | 隨然說是 #56 Merge Intervals 的延伸，但也不見得就做得出這題 |
| [62](https://leetcode.com/problems/unique-paths/) | Unique Paths | Medium | :star: | `P&C` | 基本排列組合數學題 |
| [70](https://leetcode.com/problems/climbing-stairs/) | Climbing Stairs | Easy | :star: | `recursive` | recursive 的課本基礎範例 |
| [73](https://leetcode.com/problems/set-matrix-zeroes/) | Set Matrix Zeroes | Medium | :star: | `2darray` | 應該算 Easy，沒什麼特別的... |
| [76](https://leetcode.com/problems/minimum-window-substring/) | Minimum Window Substring | Hard | :star::star::star: | `2pointer` | 想法本身是 Medium 等級，因為題目有說要 O(m + n) 的解，這反而成了提示，所以想法本身不難。不過要實作因為有好些細節所以可以到 Hard |
| [79](https://leetcode.com/problems/word-search/) | [Word Search](https://chungchris.github.io/2020/07/23/software/leetcode/Word-Search/) | Medium | :star::star::star::star: | `2darray` `dfs` | 想法不難，但要成功寫實作出來有可能會卡住 |
| [91](https://leetcode.com/problems/decode-ways/) | Decode Ways | Medium | :star: | `string` | 沒什麼特別的，幾乎算 Easy 吧 |
| [98](https://leetcode.com/problems/validate-binary-search-tree/) | Validate Binary Search Tree | Medium | :star::star::star::star: | `tree` `dfs` `bst` | 雖然星星多，但只是因為他是演算法基本功，這其實不應該是刷題了才會的東西，所以他應該要歸在 Easy。課本上判斷一個 BST 是否合法的方法就是用 DFS 的 in-order traversal 看數字是否遞增 |
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |

---

## Programcreek- Algorithm Problem Classification

[Link of the Original List](https://www.programcreek.com/2013/08/leetcode-problem-classification/)

### Array / String


### Matrix


### Linked List


### Tree


### Stack


### DFS


### Dynamic Programming


### Data Structure Design


### Bit Manipulation


### Graph


### Math/Numbers


---

## PTT tnfshjcc

[Link of the Original List](https://pttcareers.com/Soft_Job/1VTWa4HV)

---

## Kohei Arai

[Link of the Original List](https://medium.com/@koheiarai94/60-leetcode-questions-to-prepare-for-coding-interview-8abbb6af589e)

---

## Others

| # | Title | Level | Star | Tag | Comments |
|---|-------|-------|------|-----|----------|
| [HackerRank](https://www.hackerrank.com/challenges/sorting-array-of-strings/problem) | Sorting Array of Strings | Hard | :star::star::star::star: | `string` `sort` | 基本 sort string 演算法的實作 |
| [HackerRank](https://www.hackerrank.com/challenges/permutations-of-strings/problem) | [Permutations of Strings](https://chungchris.github.io/2020/07/31/software/leetcode/Permutations-of-Strings/) | Medium | :star::star::star::star: | `string` `P&C` | 產生所有排列組合的其中一種演算法 |
| [HackerRank](https://www.hackerrank.com/challenges/deque-stl/problem) | [Sliding Window Maximum](https://chungchris.github.io/2020/08/09/software/leetcode/Sliding-Window-Maximum/) | Medium | :star::star::star::star: | `2pointer` | Slide window. O(n) 解沒做過不容易想到 |
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |
