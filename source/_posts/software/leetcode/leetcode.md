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
不過刷題對我來說有另外一個意義是維持語言的手感，不然有時候久沒打一個語言就會失去感覺

於是我去找了別人推薦的清單，想說如果真的要刷就做一些比較有意義的題目，並且做完後也依據我自己的刷題目的和偏好星星

* :star: 因為都已經是有人推薦的題目，所以即使我覺得只有一顆星他依舊是一題有人推的題目，只是我自己不覺得需要刷，有可能很簡單。也有可能是我覺得就算會了也意義不大
* :star::star: 代表我覺得有時間再看就好
* :star::star::star: 代表可以刷
* :star::star::star::star: 代表一定刷
* :star::star::star::star::star: 代表多刷幾次甚至把所有 solution 都弄懂

所以以下就根據別人的推薦 list 來分別把題目都列一下

---

## Blind Curated 75

> [Link of the Original List](https://leetcode.com/list/xoqag3yj/)

| # | Title | Level | Star | Tag | Comments |
|---|-------|-------|------|-----|----------|
| [1](https://leetcode.com/problems/two-sum/) | **Two Sum** | Easy | :star: | `array` | 小暖身而已 |
| [3](https://leetcode.com/problems/longest-substring-without-repeating-characters/) | [Longest Substring Without Repeating Characters](https://chungchris.github.io/2020/07/07/software/leetcode/Longest-Substring-Without-Repeating-Characters/) | Medium | :star::star::star: | `string` | 可與 [#340 Longest Substring with At Most K Distinct Characters](https://www.cnblogs.com/grandyang/p/5351347.html) 對照。因為本題是要求完全不重複，所以我們的 map 是記 char 出現過的 index，這讓我再發現重複的時候，可以直接調整 window 的 head 到新的位置；但 340 是容許 k 次重複，所以若我們的 map 改記得的是 char 出現的次數，那每當重複次數超過容許值因而要調整 head，就要一步一步調，當然如果 map 是把每個 char 出現的位置都記錄下來，自然也就可以一次調到位。有就是說 340 其實比較有 slide window 的感覺，但本題其實不需要明確有 window 的概念，而是比較像維護一個 valid head 的 index 就可以 |
| [5](https://leetcode.com/problems/longest-palindromic-substring/) | [Longest Palindromic Substring](https://chungchris.github.io/2020/07/08/software/leetcode/Longest-Palindromic-Substring/) | Medium | :star::star: | `dp` `string` | 不算難，但可以作為 DP 的題目，或者是進階演算法有興趣的可以藉此研究 Manacher’s algorithm |
| [11](https://leetcode.com/problems/container-with-most-water/) | [Container With Most Water](https://chungchris.github.io/2020/07/08/software/leetcode/Container-With-Most-Water/) | Medium | :star::star::star: | `2pointer` | 暴力解很容易，進階解不一定想到到。但只要先以左右兩側為第一個選擇，想辦法去提升容量，演算法就很容易出來 |
| [15](https://leetcode.com/problems/3sum/) | [3Sum](https://chungchris.github.io/2020/07/09/software/leetcode/3sum/) | Medium | :star::star::star: | `array` | 暴力解就是 `O(n^3)`，進階解不一定想到到。想有比 `O(n^3)` 快的方法，掃一次是一定需要的，所以至少有 n，重點在於掃一次取得第一個數後，怎麼挑另外兩個數，並且不能是 `O(n^2)`，所以合理可以想到嘗試先 sort 過，那接下來就會變得跟 [#11 Container With Most Water](https://chungchris.github.io/2020/07/08/software/leecode/Container-With-Most-Water/) 很像。但另外還有檢查重複的部分也需要一些琢磨 |
| [19](https://leetcode.com/problems/remove-nth-node-from-end-of-list/) | [Remove Nth Node From End of List](https://chungchris.github.io/2020/07/09/software/leetcode/Remove-Nth-Node-From-End-of-List/) | Medium | :star::star::star: | `ll` | 暴力解很容易，進階解要想一下，也是個典型 2-pointer 問題。如果對 Rust 有興趣的可以就本題了解一些 Rust 的限制。fast slow 是 2-pointer 問題中常見的技巧 |
| [20](https://leetcode.com/problems/valid-parentheses/) | [Valid Parentheses](https://chungchris.github.io/2020/07/10/software/leetcode/Valid-Parentheses/) | Easy | :star::star::star::star::star: | `string` | 經典題，很容易，但如果沒有做過還是可能會漏一些東西 |
| [21](https://leetcode.com/problems/merge-two-sorted-lists/) | [Merge Two Sorted Lists](https://chungchris.github.io/2020/07/11/software/leetcode/Merge-Two-Sorted-Lists/) | Easy | :star::star::star::star::star: | `ll` `sort` | 很容易，但 Python 的 List 太強大，用久了可能會忘了在有限制的單向串列狀況下怎麼處理這個問題。同時這也是一題可以觀察 Rust 語言特性的題目 |
| [23](https://leetcode.com/problems/merge-k-sorted-lists/) | [Merge k Sorted Lists](https://chungchris.github.io/2020/07/11/software/leetcode/Merge-k-Sorted-Lists/) | Hard | :star::star::star::star::star: | `ll` `sort` | 其實應該不到 Hard，尤其是如果已經做過 [#21 Merge Two Sorted Lists](https://chungchris.github.io/2020/07/11/software/leetcode/Merge-Two-Sorted-Lists/)。把 merge 2 個轉換成 merge k 個並沒有額外需要什麼高明想法，唯一可能就是先把他們維持排序好，這樣選下一個的時候就很好選。更高級一點就是用 heap 來維護這些 list 的順序 |
| [33](https://leetcode.com/problems/search-in-rotated-sorted-array/) | [Search in Rotated Sorted Array](https://chungchris.github.io/2020/07/12/software/leetcode/Search-in-Rotated-Sorted-Array/) | Medium | :star::star::star::star: | `search` | 需要一點觀察，有可能會走錯方向，另外大於小於其實也有點煩 |
| [39](https://leetcode.com/problems/combination-sum/) | [Combination Sum](https://chungchris.github.io/2020/07/12/software/leetcode/Combination-Sum/) | Medium | :star::star::star: | `sum` | 只要注意到數字可以被重複使用，那這題蠻直觀的其實 |
| [48](https://leetcode.com/problems/rotate-image/) | [Rotate Image](https://chungchris.github.io/2020/07/12/software/leetcode/Rotate-Image/) | Medium | :star::star::star: | `2darray` | 兩種解法，一種很容易看到眼花，一種要做過才知道 |
| [49](https://leetcode.com/problems/group-anagrams/) | [Group Anagrams](https://chungchris.github.io/2020/07/13/software/leetcode/Group-Anagrams/) | Medium | :star::star: | `string` | 土法煉鋼，沒什麼高深想法，稍有練習不同語言基本語法的作用。字元組成相同，代表每一個 char 使用的次數相同，所以問題就轉變為替每一種樣式創造一個 unique 的 identifier |
| [53](https://leetcode.com/problems/maximum-subarray/) | [Maximum Subarray](https://chungchris.github.io/2020/07/19/software/leetcode/Maximum-Subarray/) | Easy | :star::star::star: | `sum` | 我覺得這題可能也可以 Medium。會被歸在 Easy 大概是因為 `O(n)` 解就是很典型的這類 array 題 greedy 的思路，他是一題經典的基本 DP (Dynamic Programming) 題，也就是存在 `f(i) = g(f(i-1))` 這樣的特性，本題的 `g` 就是 `max(f(i-1)+nums[i], nums[i])`。另外有 `O(n log n)` 的 divide and conquer 解可能是直觀一點的解，是一個典型的 divide and conquer 範例，所以可能也是另一個歸在 Easy 的原因吧 |
| [54](https://leetcode.com/problems/spiral-matrix/) | **Spiral Matrix** | Medium | :star: | `2darray` | 就分四組細心處理完就好，沒什麼 |
| [55](https://leetcode.com/problems/jump-game/) | [Jump Game](https://chungchris.github.io/2020/07/20/software/leetcode/Jump-Game/) | Medium | :star::star::star::star: | `sum` | 培養認出適合用 Greedy 演算法的問題類型，DP (dynamic programming) 和 Greedy 都是算處理本題蠻直觀的想法，但先想到哪一個就有可能導致造盲區而想不到另一個 |
| [56](https://leetcode.com/problems/merge-intervals/) | [Merge Intervals](https://chungchris.github.io/2020/07/21/software/leetcode/Merge-Intervals/) | Medium | :star::star::star: | `interval` | 想出解法容易，但可能會瑣碎，不一定能直接想到最簡潔的解法，讓其變簡潔的關鍵就是要先把 intervals 據其起點做 sort |
| [57](https://leetcode.com/problems/insert-interval/) | [Insert Interval](https://chungchris.github.io/2020/07/21/software/leetcode/Insert-Interval/) | Medium | :star::star::star: | `interval` | 隨然說是 [#56 Merge Intervals](https://chungchris.github.io/2020/07/21/software/leetcode/Merge-Intervals/) 的延伸，但也不見得就做得出這題 |
| [62](https://leetcode.com/problems/unique-paths/) | **Unique Paths** | Medium | :star::star::star: | `p&c` | 基本排列組合數學題 |
| [70](https://leetcode.com/problems/climbing-stairs/) | **Climbing Stairs** | Easy | :star: | `recursive` | recursive 的課本基礎範例 |
| [73](https://leetcode.com/problems/set-matrix-zeroes/) | **Set Matrix Zeroes** | Medium | :star: | `2darray` | 應該算 Easy，沒什麼特別的... |
| [76](https://leetcode.com/problems/minimum-window-substring/) | **Minimum Window Substring** | Hard | :star::star::star: | `2pointer` | 想法本身是 Medium 等級，因為題目有說要 `O(m + n)` 的解，這反而成了提示，所以想法本身不難。不過要實作因為有好些細節所以可以到 Hard |
| [79](https://leetcode.com/problems/word-search/) | [Word Search](https://chungchris.github.io/2020/07/23/software/leetcode/Word-Search/) | Medium | :star::star::star::star: | `2darray` `dfs` | 想法不難，但要成功寫實作出來有可能會卡住 |
| [91](https://leetcode.com/problems/decode-ways/) | **Decode Ways** | Medium | :star: | `string` | 沒什麼特別的，幾乎算 Easy 吧 |
| [98](https://leetcode.com/problems/validate-binary-search-tree/) | **Validate Binary Search Tree** | Medium | :star::star::star::star: | `tree` `dfs` `bst` | 雖然星星多，但只是因為他是演算法基本功，這其實不應該是刷題了才會的東西，所以他應該要歸在 Easy。課本上判斷一個 BST 是否合法的方法就是用 DFS 的 in-order traversal 看數字是否遞增。頂多實作上需要注意的是 recursion 中 return 的效果，如果發現不合法應該就可以直接終止，不用全部走完 |
| [100](https://leetcode.com/problems/same-tree/) | **Same Tree** | Easy | :star::star::star::star: | `tree` | 就...基本題 |
| [102](https://leetcode.com/problems/binary-tree-level-order-traversal/) | **Binary Tree Level Order Traversal** | Medium | :star::star::star::star: | `tree` `bfs` | BFS order 使用 queue 來 traverse 是基本概念，但他要把同一 level 的都明確搜集在一起，這就需要另外有方法意識到當前的 level，所以歸在 medium |
| [104](https://leetcode.com/problems/maximum-depth-of-binary-tree/) | **Maximum Depth of Binary Tree** | Easy | :star::star::star: | `tree` `dfs` | 就...基本題 |
| [105](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/) | [Construct Binary Tree from Preorder and Inorder Traversal](https://chungchris.github.io/2020/08/28/software/leetcode/Construct-Binary-Tree-from-Preorder-and-Inorder-Traversal/) | Medium | :star::star::star::star: | `tree` `dfs` | 算是經典題吧，解法沒做過不容易想到，要特別記一下 |
| [121](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/) | **Best Time to Buy and Sell Stock** | Easy | :star::star::star::star: | `array` | 暴力解就是 `O(n^2)`，但再仔細想一下其實走一輪就可以有答案，強迫自己以人類的角度走一輪就要得出答案，演算法自然就出來了 |
| [124](https://leetcode.com/problems/binary-tree-maximum-path-sum/) | Binary Tree Maximum Path Sum | Hard |  |  |  |
| [125](https://leetcode.com/problems/valid-palindrome/) | **Valid Palindrome** | Easy | :star: | `string` `2pointer` | 沒什麼... |
| [128](https://leetcode.com/problems/longest-consecutive-sequence/) | **Longest Consecutive Sequence** | Medium | :star::star::star: | `array` | 題目規定要 `O(n)`，如果不做 sort 那感覺一定大於 `O(n)`，如果做 sort 那至少 `O(nlogn)`，所以如果一定要 `O(n)`，就一定是需要額外記憶體，並且可能是 `O(2n)` 之類的。先將其轉成 set 或 hashmap，這可以在 O(n) 完成，然後在 traverse 一次就可以有解 |
| [133](https://leetcode.com/problems/clone-graph/) | **Clone Graph** | Medium | :star::star::star: | `graph` | 沒什麼太特別的，注意不要無窮 recursion 就行了 |
| [139](https://leetcode.com/problems/word-break/) | **Word Break** | Medium | :star::star::star::star: | `string` | 用 DP 分解這很直覺，但 iteration 應該選用走 wordDict 會比走 s 省時間。並且還有兩個優化，一是先把 wordDict decending sort；二是記住嘗試過哪個 offset 開始是無解的 |
| [141](https://leetcode.com/problems/linked-list-cycle/) | **Linked List Cycle** | Easy | :star::star::star: | `ll` | 沒什麼，是可以用額外的 set 來記有沒有走過某的 node，不過其實這種題典型應該是用 slow fast pointer，就不需要額外空間 |
| [143](https://leetcode.com/problems/reorder-list/) | **Reorder List** | Medium | :star::star::star::star: | `ll` | `O(3n)` 也算 `O(n)`，分成三步做，1 找到中點切成兩個 list，2 把後半部 reverse，3 merge 兩個 list |
| [152](https://leetcode.com/problems/maximum-product-subarray/) | **Maximum Product Subarray** | Medium | :star::star::star::star: | `array` | code 幾行就可以完成，主要是演算法要想出來。關鍵是 contiguous，對於這種有 contiguous 限制的題目特性，有一種最為常見的思考模式需要訓練一下。另外就本題來說，思考邏輯上可以是漸進式的，首先想如果都是非 0 正整數該怎麼解，然後想如果有 0 該怎麼解，最後再想如果有負數該怎麼解 |
| [153](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/) | **Find Minimum in Rotated Sorted Array** | Medium | :star::star::star: | `search` | 與 [#33 Search in Rotated Sorted Array](https://chungchris.github.io/2020/07/12/software/leetcode/Search-in-Rotated-Sorted-Array/) 相同且更容易 |
| [190](https://leetcode.com/problems/reverse-bits/) | **Reverse Bits** | Easy | :star::star::star: | `bit` | 沒什麼 |
| [191](https://leetcode.com/problems/number-of-1-bits/) | **Number of 1 Bits** | Easy | :star: | `bit` | 沒什麼 |
| [198](https://leetcode.com/problems/house-robber/) | [House Robber](https://chungchris.github.io/2020/09/18/software/leetcode/House-Robber/) | Medium | :star::star::star::star: | `dp` | 典型 DP 題，另外有關鍵是識別出可將 DP 的 recursive function call 簡化為 iteration。DP 問題只的就是每一的解會跟其他輪有關係，若後面輪次的解只跟其前面輪次有關係，那自然就可以用 iteration 即可毋需使用到 recursion |
| [200](https://leetcode.com/problems/number-of-islands/) | **Number of Islands** | Medium | :star::star::star: | `2darray` | 不難想，想到後也不難做出來 |
| [206](https://leetcode.com/problems/reverse-linked-list/) | **Reverse Linked List** | Easy | :star::star::star::star: | `ll` | 基本題，recursion 和 iteration 作法都熟悉一下 |
| [207](https://leetcode.com/problems/course-schedule/) | **Course Schedule** | Medium | :star::star::star::star: | `graph` `dfs` | 偵測單向圖中是否有循環的基本題。Recursive 走 DFS，如果走到重複的就是有循環。雖然每一個點都要為 root 去走 DFS 以避免漏掉獨立的迴圈，但可以另外透過記憶哪些點已經檢查通過了來避免重工 |
| [208](https://leetcode.com/problems/implement-trie-prefix-tree/) | **Implement Trie (Prefix Tree)** | Medium | :star::star::star: | `string` `bst` | 沒什麼，就是 string 版本的 binary search tree |
| [211](https://leetcode.com/problems/design-add-and-search-words-data-structure/) | **Design Add and Search Words Data Structure** | Medium | :star::star::star: | `string` | 可以用 map/dict 來實作，概念上其實變得像一棵樹 |
| [212](https://leetcode.com/problems/word-search-ii/) | Word Search II | Hard |  |  |  |
| [213](https://leetcode.com/problems/house-robber-ii/) | **House Robber II** | Medium | :star::star::star: | `dp` | 相較於 [#198 House Robber](https://chungchris.github.io/2020/09/18/software/leetcode/House-Robber/) 因為變成 circle 所以需要多一個關鍵想法：對於本題存在一個最佳解，那我們若任選一點 x，該點 x 只有『存在於最佳解中』和『不存在於最佳解中』兩種可能性；對於 x 不存在於最佳解中的這種可能行，本題的最佳解形同由 x+1 ~ x-1 這個 circle 的最佳解；而對於 x 存在於最佳解中的這種可能性，本題的最佳解形同由 x ~ x-2 這個 circle 的最佳解。所以若我們將 x 選為 0，那只要針對 arr[0:n-1] 和 arr[1:n] 分別去求解，再取其大者即可 |
| [217](https://leetcode.com/problems/contains-duplicate/) | **Contains Duplicate** | Easy | :star: | `array` | 暖身吧... |
| [226](https://leetcode.com/problems/invert-binary-tree/) | **Invert Binary Tree** | Easy | :star: | `tree` | 暖身吧... |
| [230](https://leetcode.com/problems/kth-smallest-element-in-a-bst/) | **Kth Smallest Element in a BST** | Medium | :star::star::star: | `tree` `bst` `dfs` | 基本題的小延伸吧。從小排到大就是 in-order traversal，那自然可以知道第 k 小的是誰，加上一些優化就可以在得到第 k 個時就不要再繼續走下去 |
| [235](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/) | **Lowest Common Ancestor of a Binary Search Tree** | Easy | :star::star::star: | `tree` `bst` | 沒什麼，他是一個 BST，比 root 小的在左邊，比 root 大的在右邊，如果 p,q 都比 root 小，那 LCA 就往左邊找，反之往右邊；如果 root 值介於 p,q 之間，那 p,q 分別在 root 的左右子樹，此時找 LCA 不可能往左右字數去移動，因為一但移動，必定會失去 p,q 其中之一，這也代表 root 本身就是 LCA |
| [238](https://leetcode.com/problems/product-of-array-except-self/) | **Product of Array Except Self** | Medium | :star::star::star: | `array` | 沒做過不一定想得到，關鍵想法在 prefix 和 suffix，prefix/suffix 指的是某一個數 前面所有數/後面所有數，每一個數對應的解就是 `product of prefix` 乘上 `product of suffix`。如此一來 2 passes 就可以解出來，時間複雜度就是 `O(n)`，並且只需要額外一塊等長的 space |
| [242](https://leetcode.com/problems/valid-anagram/) | **Valid Anagram** | Easy | :star::star::star: | `string` | [#49 Group Anagrams](https://chungchris.github.io/2020/07/13/software/leetcode/Group-Anagrams/) 的基礎題 |
| [](https://leetcode.com/problems/meeting-rooms/) | [Meeting Rooms]() | Medium |  |  |  |
| [](https://leetcode.com/problems/meeting-rooms-ii/) | [Meeting Rooms II]() | Medium |  |  |  |
| [](https://leetcode.com/problems/graph-valid-tree/) | [Graph Valid Tree]() | Medium |  |  |  |
| [268](https://leetcode.com/problems/missing-number/) | **Missing Number** | Easy | :star::star::star: | `array` | 跟 [#41 First Missing Positive](https://chungchris.github.io/2020/08/13/software/leetcode/First-Missing-Positive/) 一樣，用 2n space 會很簡單，用 n space 會稍難一點 |
| [](https://leetcode.com/problems/alien-dictionary/) | [Alien Dictionary]() | Medium |  |  |  |
| [](https://leetcode.com/problems/encode-and-decode-strings/) | [Encode and Decode Strings]() | Medium |  |  |  |
| [295](https://leetcode.com/problems/find-median-from-data-stream/) | [Find Median from Data Stream]() | Hard |  |  |  |
| [297](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/) | [Serialize and Deserialize Binary Tree]() | Hard |  |  |  |
| [300](https://leetcode.com/problems/longest-increasing-subsequence/) | [Longest Increasing Subsequence](https://chungchris.github.io/2020/10/17/software/leetcode/Longest-Increasing-Subsequence/) | Medium | :star::star::star: | `array` | 這種 array 類型一慣的思路都是用 greedy，就是從頭走，一步一步多看一個數字，然後去更新最佳解。但即使這題也是相同，不過沒做過還是不容易想到做法 |
| [322](https://leetcode.com/problems/coin-change/) | **Coin Change** | Medium | :star::star::star::star: | `array` | 第一個誤區是 greedy 無法解這題，例如 `[23, 22, 7 ,1]` 找 `29`，用 greedy 會得到 `23+1+1+1+1+1+1`，但其實 `22+7` 才是最佳解。但我們知道這題還是 DP，DP 就是前後輪次有關聯，比如 `f(n) = g(f(n-i))`，而實作就有兩種方向，若我們從 `f(n)` 出發，那就會變 recursion；若從 `f(0)` 出發，就會是 iteration |
| [](https://leetcode.com/problems/number-of-connected-components-in-an-undirected-graph/) | [Number of Connected Components in an Undirected Graph]() | Medium |  |  |  |
| [338](https://leetcode.com/problems/counting-bits/) | **Counting Bits** | Easy | :star::star::star::star: | `bit` | 暴力解就是 `O(nlogn)`，如果要 `O(n)` 解就要有分解的 DP 想法，可已歸在 Medium 等級：一個整數 `n`，假設他的 binary representation 有 5 bits，那他的 1 的數量，就會是右邊 4 bits 的 1 的數量，加上最左側是否為 1，為什麼要這樣拆解？因為最左側是否為 1 這我們直接 mod 2 就可以知道，而右邊 4 bits 的 1 的數量，因該是在前面的輪次當中就已經得到答案 |
| [347](https://leetcode.com/problems/top-k-frequent-elements/) | **Top K Frequent Elements** | Medium | :star::star::star: | `array` `heap` | 用 map 統計很容易，但統計完後要找最大的前 k 個，也就是要做 sort，且題目要 `O(nlogn)` 那就可以用 heap sort，沒有一定，只是本題特性上就也剛好也蠻適合就拿來練一下 heap sort |
| [371](https://leetcode.com/problems/sum-of-two-integers/) | **Sum of Two Integers** | Medium | :star::star::star: | `bit` | Bit operation 都不適合用 python，因為 python 的 `int` 不受限於 32 bits，他可以很大，但為達成這個，shift bit 的行為就跟其他語言不一樣。所以這題用 C 或 Java 來做：`XOR` 可以得到該 bit 的結果，`AND` 可以得到是否要進位，recursive 直到不需進位為止 |
| [417](https://leetcode.com/problems/pacific-atlantic-water-flow/) | **Pacific Atlantic Water Flow** | Medium | :star::star::star: | `2darray` | 跟 [#200 Number of Islands](https://leetcode.com/problems/number-of-islands/) 有些類似。可以反著想：從 Pacific 出發可以到達的地方，以及從 Atlantic 出發可以到達的地方，這兩個部分的交集就是答案 |
| [424](https://leetcode.com/problems/longest-repeating-character-replacement/) | **Longest Repeating Character Replacement** | Medium | :star::star::star::star: | `array` | 一樣是 greedy slide window 可以解的問題，關鍵在於要認知到調整 window head 的時機是目前 window length 減去目前 window 中最常出現的那個字的次數超過 k 的時候，這代表我們就算用去 k 次機會也無法把目前的 window 都變成同一個字，所以這就是一個要縮 head 的來 recover 的時機 |
| [435](https://leetcode.com/problems/non-overlapping-intervals/) | **Non-overlapping Intervals** | Medium | :star::star::star: | `interval` | 與 [#56 Merge Intervals](https://chungchris.github.io/2020/07/21/software/leetcode/Merge-Intervals/) 類似 |
| [572](https://leetcode.com/problems/subtree-of-another-tree/) | **Subtree of Another Tree** | Easy | :star::star::star::star: | `tree` | 要實際做一下 |
| [647](https://leetcode.com/problems/palindromic-substrings/) | **Palindromic Substrings** | Medium | :star::star::star: | `string` | 跟 [#5 Longest Palindromic Substring](https://chungchris.github.io/2020/07/08/software/leetcode/Longest-Palindromic-Substring/) 類似，就是細心一點做吧... |

---

## Programcreek- Algorithm Problem Classification

> [Link of the Original List](https://www.programcreek.com/2013/08/leetcode-problem-classification/)

### Array / String

| # | Title | Level | Star | Tag | Comments |
|---|-------|-------|------|-----|----------|
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |

### Matrix

| # | Title | Level | Star | Tag | Comments |
|---|-------|-------|------|-----|----------|
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |

### Linked List

| # | Title | Level | Star | Tag | Comments |
|---|-------|-------|------|-----|----------|
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |

### Tree

| # | Title | Level | Star | Tag | Comments |
|---|-------|-------|------|-----|----------|
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |

### Stack

| # | Title | Level | Star | Tag | Comments |
|---|-------|-------|------|-----|----------|
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |

### DFS

| # | Title | Level | Star | Tag | Comments |
|---|-------|-------|------|-----|----------|
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |

### Dynamic Programming

| # | Title | Level | Star | Tag | Comments |
|---|-------|-------|------|-----|----------|
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |

### Data Structure Design

| # | Title | Level | Star | Tag | Comments |
|---|-------|-------|------|-----|----------|
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |

### Bit Manipulation

| # | Title | Level | Star | Tag | Comments |
|---|-------|-------|------|-----|----------|
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |

### Graph

| # | Title | Level | Star | Tag | Comments |
|---|-------|-------|------|-----|----------|
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |

### Math/Numbers

| # | Title | Level | Star | Tag | Comments |
|---|-------|-------|------|-----|----------|
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |

---

## Microsoft

### IGotAnOffer

> [Microsoft software engineer interview: the only post you'll need to read](https://pttcareers.com/Soft_Job/1VTWa4HV)
> the post is summarizing 250 feedbacks on Glassdoor. 

#### Arrays / Strings (36% of questions, most frequent)

| # | Title | Level | Star | Tag | Comments |
|---|-------|-------|------|-----|----------|
| [151](https://leetcode.com/problems/reverse-words-in-a-string/) | **Reverse Words in a String** | Medium | :star::star::star: | `string` | 如果知道使用 split() 和 join() 會只需要 1 行。如果不知道或不能用，那從尾開始走會比從頭開始走好 |
| [1](https://leetcode.com/problems/two-sum/) | **Two Sum** | Easy | :star: | `array` | 應該只是暖身... |
| [79](https://leetcode.com/problems/word-search/) | [Word Search](https://chungchris.github.io/2020/07/23/software/leetcode/Word-Search/) | Medium | :star::star::star::star: | `2darray` `dfs` | 想法不難，但要成功寫實作出來有可能會卡住 |
| [41](https://leetcode.com/problems/first-missing-positive/) | [First Missing Positive](https://chungchris.github.io/2020/08/13/software/leetcode/First-Missing-Positive/) | Hard | :star::star::star::star: | `array` | 題目規範 `O(n)`，需要意識到數列長度是一個可以在 `O(n)` 取得的資訊，`O(2n)` 也是算 `O(n)`。一但長度也是已知資訊，要想出演算法概念不難，不過要實作出來可能會遇到鬼打牆的狀況，所以還是可以算是一題 Hard 等級的題目 |
| [722](https://leetcode.com/problems/remove-comments/) | **Remove Comments** | Medium | :star::star: | `string` | 我覺得如果白板考這一題會非常難做對，除非已經寫過至少三四次；我也覺得不太可能純白板場合考這題，因為人工跑 test case 很容易眼花撩亂，如果是可以電腦跑 test case 的環境比較有可能考到。我認為這題最簡潔的寫法[只有一種](https://leetcode.com/problems/remove-comments/discuss/295158/Simple-Python-solution)，如果沒有把這種寫法細節都記下來，其他比他更瑣碎一點的寫法都可能導致在更多細節有錯誤 |
| [54](https://leetcode.com/problems/spiral-matrix/) | **Spiral Matrix** | Medium | :star: | `2darray` | 就分四組細心處理完就好，沒什麼 |
| [443](https://leetcode.com/problems/string-compression/) | **String Compression** | Medium | :star::star::star: | `string` `array` | 沒什麼特別的，細心做完即可，另外如果是 python 應該不會選這一題，其實只要題目規範到 string 且規定使用 Spece O(1) in-place replacement 的問題 python 都不太適用 |
| [340](https://leetcode.com/problems/longest-substring-with-at-most-k-distinct-characters/) | [Longest Substring with At Most K Distinct Characters](https://www.cnblogs.com/grandyang/p/5351347.html) | Medium | :star::star::star: | `string` | 用 map 來記住看過的東西就行，詳細一點可以用 `map<char, \[int\]>` 記所有出現的位置，但其實只需要用 `map<char, int>` 記住出現的次數就行了，實作結果來說，記所有出現的位置對於速度沒有必然性的提升 |

#### Linked lists (29%)

| # | Title | Level | Star | Tag | Comments |
|---|-------|-------|------|-----|----------|
| [25](https://leetcode.com/problems/reverse-nodes-in-k-group/) | [Reverse Nodes in k-Group](https://chungchris.github.io/2020/08/15/software/leetcode/Reverse-Nodes-in-k-Group/) | Hard | :star::star::star::star::star: | `ll` | Revert linked list 升級版 |
| [138](https://leetcode.com/problems/copy-list-with-random-pointer/) | [Copy List with Random Pointer](https://chungchris.github.io/2020/08/19/software/leetcode/Copy-List-with-Random-Pointer/) | Medium | :star::star::star::star: | `ll` | 不使用額外空間的解法沒做過不容易想到 |
| [23](https://leetcode.com/problems/merge-k-sorted-lists/) | [Merge k Sorted Lists](https://chungchris.github.io/2020/07/11/software/leetcode/Merge-k-Sorted-Lists/) | Hard | :star::star::star::star::star: | `ll` `sort` | 其實應該不到 Hard，尤其是如果已經做過 [#21 Merge Two Sorted Lists](https://chungchris.github.io/2020/07/11/software/leetcode/Merge-Two-Sorted-Lists/)。把 merge 2 個轉換成 merge k 個並沒有額外需要什麼高明想法 |
| [24](https://leetcode.com/problems/swap-nodes-in-pairs/) | **Swap Nodes in Pairs** | Medium | :star::star::star: | `ll` | 不難但就是把圖畫出來細心一點確認每個該改的都有改到 |
| [148](https://leetcode.com/problems/sort-list/) | [Sort List](https://chungchris.github.io/2020/08/19/software/leetcode/Sort-List/) | Medium | :star::star::star::star::star: | `ll` `sort` | 是 [#21 Merge Two Sorted Lists](https://chungchris.github.io/2020/07/11/software/leetcode/Merge-Two-Sorted-Lists/) 的延伸，題目規範的 `O(n logn)` time and `O(1)` memory (i.e. constant space) 反而成為提示 |

#### Graphs / Trees (20%)

(樹/圖 的題目不太可能 online 考，畫就搞半天... 面對面的 onsite 比較有可能)

| # | Title | Level | Star | Tag | Comments |
|---|-------|-------|------|-----|----------|
| [105](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/) | [Construct Binary Tree from Preorder and Inorder Traversal](https://chungchris.github.io/2020/08/28/software/leetcode/Construct-Binary-Tree-from-Preorder-and-Inorder-Traversal/) | Medium | :star::star::star::star: | `tree` `dfs` | 算是經典題吧，解法沒做過不容易想到，要特別記一下 |
| [297](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/) | [Serialize and Deserialize Binary Tree]() | Hard |  |  |  |
| [428](https://leetcode.com/problems/serialize-and-deserialize-n-ary-tree/) | [Serialize and Deserialize N-ary tree]() | Hard |  |  |  |
| [285](https://leetcode.com/problems/inorder-successor-in-bst/) | **Inorder Successor in BST** | Medium | :star::star::star: | `tree` `dfs` | 我們知道 BST 的 inorder traversal 就是會產生由小到大的數列。所以暴力解就是把整個 inorder traversal 走完，自然知道 successor 是誰，那就是 `O(n)`。不過因為本身是一個 BST，所以理論上找到目標應該是 `O(h)` 就夠。所以進階解就是從 BST root 開始走，逐漸逼近目標，把前一級的那個比目標大的點記住；這個記憶的值會一直縮小，因為你是一直在逼近目標當中，所以途經的任何比目標大的點，他的趨勢都是會一直變小。抵達目標之後，我們知道他的 inorder successor 就是會比他恰恰稍大一點：所以若此目標點有右子樹，那就會是右子樹當中的最小值；如果此目標點沒有右子樹，那他的 successor 就會是我們曾經走過的記憶的那個點；如果也不存在這個點，也就是沒有 successor，代表目標已經是 BST 中的最大值 |
| [510](https://leetcode.com/problems/inorder-successor-in-bst-ii/) | [Inorder Successor in BST II]() | Medium |  |  |  |
| [98](https://leetcode.com/problems/validate-binary-search-tree/) | **Validate Binary Search Tree** | Medium | :star::star::star::star: | `tree` `dfs` `bst` | 雖然星星多，但只是因為他是演算法基本功，這其實不應該是刷題了才會的東西，所以他應該要歸在 Easy。課本上判斷一個 BST 是否合法的方法就是用 DFS 的 in-order traversal 看數字是否遞增 |
| [450](https://leetcode.com/problems/delete-node-in-a-bst/) | **Delete Node in a BST** | Medium | :star::star::star::star: | `tree` `dfs` `bst` | 演算法部分是課本基礎，但實作有個關鍵是找到要上移的節點之後，可以繼續使用我們的 delete() 去把要上移的節點從子樹中刪除 |
| [797](https://leetcode.com/problems/all-paths-from-source-to-target/) | **All Paths From Source to Target** | Medium | :star::star::star::star: | `graph` `dfs` `dag` | 一個有向無循環圖要找所有 path，無循環我們就可以用 greedy，所以演算法部分不難。實作部分，每次的 recursion，用回傳值來傳遞 path 比較不好，因為視語言使用的資料結構有可能在 push_frount() 這種操作有負擔，所以維護一個 global 的 path/paths，動態的 push/pop 是較佳的做法 |

#### Search / Sort (6%)

| # | Title | Level | Star | Tag | Comments |
|---|-------|-------|------|-----|----------|
| [200](https://leetcode.com/problems/number-of-islands/) | **Number of Islands** | Medium | :star::star::star: | `2darray` | 不難想，想到後也不難做出來 |
| [116](https://leetcode.com/problems/populating-next-right-pointers-in-each-node/) | **Populating Next Right Pointers in Each Node** | Medium | :star::star::star: | `tree` `bfs` | 跟 [[#102 Binary Tree Level Order Traversal]](https://leetcode.com/problems/binary-tree-level-order-traversal/) 差不多 |
| [4](https://leetcode.com/problems/median-of-two-sorted-arrays/) | **Median of Two Sorted Arrays** | Hard | :star::star::star: | `array` `sort` | Medium 而已吧，跟 [[#21 Merge Two Sorted Lists]](https://chungchris.github.io/2020/07/11/software/leetcode/Merge-Two-Sorted-Lists/) 差不多 |
| [74](https://leetcode.com/problems/search-a-2d-matrix/) | **Search a 2D Matrix** | Medium | :star::star::star: | `2darray` `search` | 不難，一樣想成一維 binary search 只要在 index 上做簡單換算就行 |

#### Dynamic programming (5%)

| # | Title | Level | Star | Tag | Comments |
|---|-------|-------|------|-----|----------|
| []() | []() | Medium | :star: |  |  |

#### Bit manipulation / Maths (4% of questions, least frequent)

(這種題型基本上是選 C/C++ 才會有啦，不然用 python 搞 bit operation 沒什麼意思)

| # | Title | Level | Star | Tag | Comments |
|---|-------|-------|------|-----|----------|
| [1239](https://leetcode.com/problems/maximum-length-of-a-concatenated-string-with-unique-characters/) | [Maximum Length of a Concatenated String with Unique Characters](https://chungchris.github.io/2020/08/27/software/leetcode/Maximum-Length-of-a-Concatenated-String-with-Unique-Characters/) | Medium | :star::star::star::star: | `p&c` `string` `bit` | 兩個關鍵，第一個是要會由一個給定的 list 去列出所有的無序組合；第二個是透過 bit 來取代 map 做記憶可以節省記憶體，不過如果是 python 這麽高階的語言，就不要搞什麼 bit operation 了，記憶體就給他用下去，直接用 set 來記憶就行了 |
| [136](https://leetcode.com/problems/single-number/) | **Single Number** | Easy | :star::star: | `bit` `xor` | 就是需要記得 XOR 的特性... `n^n=0`, `n^0=n`, `a^b^c=b^c^a`，所以 `a^b^c^d^b^a^d = a^a^b^b^c^d^d = 0^0^c^0 = c` |

---

## Amazon

### ayedot

> [All Paid Amazon Leetcode Question for Interview and Practice For Free](https://ayedot.com/62/MiniBlog/All-Paid-Amazon-Leetcode-Question-for-Interview-and-Practice-For-Free)

| # | Title | Level | Star | Tag | Comments |
|---|-------|-------|------|-----|----------|
| [1](https://leetcode.com/problems/two-sum/) | **Two Sum** | Easy | :star: | `array` | 小暖身而已 |
| [5](https://leetcode.com/problems/longest-palindromic-substring/) | [Longest Palindromic Substring](https://chungchris.github.io/2020/07/08/software/leetcode/Longest-Palindromic-Substring/) | Medium | :star::star: | `dp` `string` | 不算難，但可以作為 DP 的題目，或者是進階演算法有興趣的可以藉此研究 Manacher’s algorithm |
| [21](https://leetcode.com/problems/merge-two-sorted-lists/) | [Merge Two Sorted Lists](https://chungchris.github.io/2020/07/11/software/leetcode/Merge-Two-Sorted-Lists/) | Easy | :star::star::star::star::star: | `ll` `sort` | 很容易，但 Python 的 List 太強大，用久了可能會忘了在有限制的單向串列狀況下怎麼處理這個問題。同時這也是一題可以觀察 Rust 語言特性的題目 |
| [22](https://leetcode.com/problems/generate-parentheses/) | **Generate Parentheses** | Medium | :star::star::star::star: | `p&c` | 自然想到 recursive，但如果是想做成 `f(n) = g(f(n-1))`，那會很難做，因為要處理重複性的問題。所以本題用 greedy 排列組合想法才比較好，從 `""` 開始把所有的能都長出來 |
| [56](https://leetcode.com/problems/merge-intervals/) | [Merge Intervals](https://chungchris.github.io/2020/07/21/software/leetcode/Merge-Intervals/) | Medium | :star::star::star: | `interval` | 想出解法容易，但可能會瑣碎，不一定能直接想到最簡潔的解法，讓其變簡潔的關鍵就是要先把 intervals 據其起點做 sort |
| [59](https://leetcode.com/problems/spiral-matrix-ii/) | **Spiral Matrix II** | Medium | :star::star: | `2darray` | 跟 [#54 Spiral Matrix](https://leetcode.com/problems/spiral-matrix/) 差不多，分四組細心處理完就好，沒什麼 |
| [63](https://leetcode.com/problems/unique-paths-ii/) | **Unique Paths II** | Medium | :star::star::star: | `2darray` | 不像 [#62 Unique Paths](https://leetcode.com/problems/unique-paths/) 可以直接算，本題要跑。兩種作法 recursion 和 iteration，兩個方向，一個是從終點往回看 `f(x,y) = f(x-1,y) + f(x,y-1)`，一個是從起點開始看 `f(x,y) = f(x+1,y) + f(x,y+1)` | |
| [64](https://leetcode.com/problems/minimum-path-sum/) | **Minimum Path Sum** | Medium | :star::star::star::star: | `2darray` `graph` | 算是 shortest path 的基本題 |
| [96](https://leetcode.com/problems/unique-binary-search-trees/) | **Unique Binary Search Trees** | Medium | :star::star::star: | `BST` | 因為本題數字不會跳躍，所以觀察一下就知道是 DP 題，也就是 `f(n)` 會與之前的 `f(n-i)` 相關 |
| [138](https://leetcode.com/problems/copy-list-with-random-pointer/) | [Copy List with Random Pointer](https://chungchris.github.io/2020/08/19/software/leetcode/Copy-List-with-Random-Pointer/) | Medium | :star::star::star::star: | `ll` | 不使用額外空間的解法沒做過不容易想到 |
| [167](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/) | **Two Sum II - Input Array Is Sorted** | Easy | :star::star::star: | `array` | 從左右兩側往內選 |
| [240](https://leetcode.com/problems/search-a-2d-matrix-ii/) | [Search a 2D Matrix II]() | Medium | :star::star::star: | `2darray` | 跟 [#74 Search a 2D Matrix](https://leetcode.com/problems/search-a-2d-matrix/) 不同，這題不是 binary search，而是需要從另一角度。 |
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |

### TECHYIELD

> [Amazon List of LeetCode Questions](https://techyield.blogspot.com/2019/07/amazon-list-of-leetcode-questions.html)

| # | Title | Level | Star | Tag | Comments |
|---|-------|-------|------|-----|----------|
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |

### alexlop

> [Amazon Final Interview Questions | SDE1](https://leetcode.com/discuss/interview-question/488887/Amazon-Final-Interview-Questions-or-SDE1)

| # | Title | Level | Star | Tag | Comments |
|---|-------|-------|------|-----|----------|
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |

### more

[Discussions on LeetCode](https://leetcode.com/discuss/explore/amazon?currentPage=1&orderBy=hot&query=)

---

## yanwei-liu

> [Top LeetCode Interview Questions](https://yanwei-liu.medium.com/top-leetcode-interview-questions-b046298a5de)

| # | Title | Level | Star | Tag | Comments |
|---|-------|-------|------|-----|----------|
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |

---

## PTT tnfshjcc

> [[心得] 我的leetcode刷題清單](https://pttcareers.com/Soft_Job/1VTWa4HV)

| # | Title | Level | Star | Tag | Comments |
|---|-------|-------|------|-----|----------|
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |

---

## Kohei Arai

> [60 LeetCode problems to solve for coding interview](https://medium.com/@koheiarai94/60-leetcode-questions-to-prepare-for-coding-interview-8abbb6af589e)

| # | Title | Level | Star | Tag | Comments |
|---|-------|-------|------|-----|----------|
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |

---

### Javarevisited

> [Top 30 Microsoft Interview Questions for Software Development Engineers (SDE)](https://javarevisited.blogspot.com/2015/12/30-microsoft-interview-questions-for-software-development-engineers.html#axzz73KhZMFyu)

| # | Title | Level | Star | Tag | Comments |
|---|-------|-------|------|-----|----------|
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |

---

### CareerCup

> [Link of the Original List](https://www.careercup.com/page?pid=microsoft-interview-questions)

| # | Title | Level | Star | Tag | Comments |
|---|-------|-------|------|-----|----------|
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |

---

## Others

| # | Title | Level | Star | Tag | Comments |
|---|-------|-------|------|-----|----------|
| [HackerRank](https://www.hackerrank.com/challenges/sorting-array-of-strings/problem) | Sorting Array of Strings | Hard | :star::star::star: | `string` `sort` | 基本 sort string 演算法的實作 |
| [HackerRank](https://www.hackerrank.com/challenges/permutations-of-strings/problem) | [Permutations of Strings](https://chungchris.github.io/2020/07/31/software/leetcode/Permutations-of-Strings/) | Medium | :star::star::star::star: | `string` `p&c` | 產生所有排列組合的其中一種演算法 |
| [HackerRank](https://www.hackerrank.com/challenges/deque-stl/problem) | [Sliding Window Maximum](https://chungchris.github.io/2020/08/09/software/leetcode/Sliding-Window-Maximum/) | Medium | :star::star::star::star: | `2pointer` | Slide window. `O(n)` 解沒做過不容易想到 |
| [179](https://leetcode.com/problems/largest-number/) | **Largest Number** | Medium | :star::star::star::star: | `array` | 需要一個簡單但常常被忽略的想法，在排列組合比大小的情境當中，如果是 greedy 可解，那我們不斷要下的決定就是下一個要選誰，所以我們會需要有邏輯去判別出 `A` 和 `B` 誰對於答案更有幫助，這個想法就會把我們導向去分析 `A` 和 `B` 的內容；但是有另外一個想法在本題更為適用，若我們現在選擇 `A`，其實就代表 `AB` 比 `BA` 更好，所以如果分析 `AB` 和 `BA` 的優劣比比較 `A` 和 `B` 的細部內容更為容易，那就應該採用這個角度 |
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |
| []() | []() | Medium | :star: |  |  |
