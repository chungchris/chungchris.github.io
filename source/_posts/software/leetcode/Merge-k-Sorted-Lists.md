---
title:
  - '[LeetCode] #23 Merge k Sorted Lists'
categories: software/leetcode
keywords:
  - linked list
  - heap sort
hide: true
summary: 融合 k 個排序過的單向串列
date: 2020-07-11 17:22:51
---

## Problem

融合 k 個排序過的單向串列

* [Link](https://leetcode.com/problems/merge-k-sorted-lists/)
* 等級：**Hard**
* 推薦指數：[:star::star::star::star::star:] 其實應該不到 Hard，尤其是如果已經做過 [[#21 Merge Two Sorted Lists](https://chungchris.github.io/2020/07/11/software/leecode/Merge-Two-Sorted-Lists/)]。把 merge 2 個轉換成 merge k 個並沒有額外需要什麼高明想法，唯一可能就是先把他們都排序好，這樣選下一個的時候就很好選。更高級一點就是用 heap 來維護這些 list 的順序

> :star: 有人推薦過的題目的我才會紀錄，所以即使我覺得只有一顆星他依舊是一題有其他人推薦的題目，只是我自己不覺得需要刷
> :star::star: 代表我覺得有時間再看就好
> :star::star::star: 代表可以刷
> :star::star::star::star: 代表一定刷
> :star::star::star::star::star: 代表多刷幾次甚至把所有 solution 都弄懂

## 想法

跟 [[#21 Merge Two Sorted Lists](https://chungchris.github.io/2020/07/11/software/leecode/Merge-Two-Sorted-Lists/)] 一樣，就是從所有 list 的頭裡面挑最小的那個拉出來
只是說當只有 2 個 list 的時候，挑小的那個只要一次 compare 就可以完成；今天可能多至有 k 個 list，怎麼從 k 個裡面挑最小的可能就會存在優化空間，不過基本想法終究沒有不同

## Source Code (Python)

### Recurisive

``` python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def mergeKLists(self, lists: List[ListNode], skip_process = False) -> ListNode:
        def process():
            new_list = []
            for n in lists: # 這裏 O(k)
                if n is not None:
                    new_list.append(n)
            return sorted(new_list, key = lambda n: n.val) # python 的 sort 可以當作他 O(klogk)

        if not skip_process:
            lists = process()

        if not lists:
            return None

        h = lists.pop(0)

        if h.next:
            i = 0
            while i < len(lists) and h.next.val > lists[i].val: # 這裏是用 Insertion(sort) 期望值就是 O(n/k)
                i += 1
            lists.insert(i, h.next)

        h.next = self.mergeKLists(lists, True)

        return h
```

假設有 `k` 個 list，一開始要做一次 sort 去排序每個 list 的頭，花了 `O(k + klogk)`
24 行是要將 `n.next` 插入合適的位置，有很多種插法，這裏就用最白癡的從頭開始找。但因為 lists 原本就已經排序過，所以其實也只要跑一輪就行，期望值就是每一個 list 的一半長度，假設總共有 `N` 個節點，大概就是 `O(N/k)`
而 `mergeKLists()` 每次只會疊一個節點，所以總共要跑 `N` 次，合起來就是 `O(N^2/k)`；再加上一開始做的 sort，就是 `O(k + klogk + N^2/k)`

### Iteration

``` python
class Solution:
    def mergeKLists(self, lists: List[ListNode]) -> ListNode:
        new_list = []
        for n in lists:
            if n is not None:
                new_list.append(n)
        new_list.sort(key = lambda n: n.val)
        
        D = ListNode()
        cur = D
        while new_list:
            n = new_list.pop(0)
            cur.next = n
            if n.next:
                i = 0
                while i < len(new_list) and n.next.val > new_list[i].val:
                    i += 1
                new_list.insert(i, n.next)
            cur = cur.next

        return D.next
```

時間複雜度跟 recurisive 沒有不同
我們可以嘗試 improve，將一開始的 sort() 和後來的 Insertion(sort)，都用一個 heap 來維護

### Heap sort

``` python
class Solution:
    def mergeKLists(self, lists: List[ListNode]) -> ListNode:
        import heapq

        trick_order = 0
        new_list = []
        for n in lists:
            if n is not None:
                heapq.heappush(new_list, (n.val, trick_order, n))
                trick_order += 1
        
        D = ListNode()
        cur = D
        while new_list:
            n = heapq.heappop(new_list)[2]
            cur.next = n
            if n.next:
                heapq.heappush(new_list, (n.next.val, trick_order, n.next))
                trick_order += 1
            cur = cur.next

        return D.next
```

python 有提供 `heapq` 這個 lib 可以直接用
`trick_order` 的使用就是個 trick... 因為 `heapq` 的 `heappush()` 沒有辦法像 `sort()`/`sorted()` 一樣直接指定 key 值；他提供的替代方案是可以餵一個 tuple，但問題是，如果 tuple 比較第一個值相等，他還是會接著比較到後面的值，因此造成 `ListNode` instance 在某些時候就會被拿來比較而產生 exception
所以我們的 trick 就是在 `ListNode` 之前的順位插入一個 unique 的整數 `trick_order`，這樣往後的比較發生的時候，比較到第二級就一定會有結果，不會需要比較到第三級 `ListNode` 因而觸發 exception

不過還是有更乾淨一點的寫法就是實作 __lt__ 方法啦，每當 python 有需要比較的時候其實就是去找該物件的的 `__eq__`, `__lt__` 方法來知道怎麼比較

``` python
class Solution:
    def mergeKLists(self, lists: List[ListNode]) -> ListNode:
        import heapq
        ListNode.__lt__ = lambda self, other: self.val < other.val

        new_list = []
        for n in lists: # O(k)
            if n is not None:
                heapq.heappush(new_list, n) # O(logk)

        D = ListNode()
        cur = D
        while new_list: # O(N)
            n = heapq.heappop(new_list) # O(1)
            cur.next = n
            if n.next:
                heapq.heappush(new_list, n.next) # O(logk)
            cur = cur.next

        return D.next
```

時間複雜度變成 `O(klogk + Nlogk)`
