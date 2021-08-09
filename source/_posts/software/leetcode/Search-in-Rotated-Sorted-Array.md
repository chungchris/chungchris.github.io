---
title:
  - '[LeetCode] #33 Search in Rotated Sorted Array'
tags:
  - leetcode:sort
  - leetcode:search
categories: software/leetcode
keywords:
  - binary search
hide: true
summary: 在一個被 rotate 過的 sorted array 做 binary search
date: 2020-07-12 13:52:25
---

## Problem

在一個被 rotate 過的 sorted array 做 binary search

* [Link](https://leetcode.com/problems/search-in-rotated-sorted-array/)
* 等級：**Medium**
* 推薦指數：[:star::star::star::star:] 需要一點觀察，有可能會走錯方向，另外大於小於其實也有點煩

> :star: 有人推薦過的題目的我才會紀錄，所以即使我覺得只有一顆星他依舊是一題有其他人推薦的題目，只是我自己不覺得需要刷
> :star::star: 代表我覺得有時間再看就好
> :star::star::star: 代表可以刷
> :star::star::star::star: 代表一定刷
> :star::star::star::star::star: 代表多刷幾次甚至把所有 solution 都弄懂

## 想法

題目規定時間要 `O(log n)`，所以我們知道一定是 binary search

那就先想一下差別在哪裡
binary search，對於一個已經 sort 過的 list，我們每次切中間，如果 target 更小，我們就知道接著要找左半，反之找右半
如果 list 是存在一個 indexing array，那我們通常會維護一個 (left, right) 的變數來記錄我們接著要搜尋的區間

但現在這題因為 list 有被 rotate 過，所以我們面臨兩個可能的方向
第一步肯定還是對半切
如果 target 更小或更大，我們是應該去想複雜一點的算法算出新的 (left, right) 值？還是應該維持原本 binary search 的行為持續使用要取左半還是取右半這種判斷即可？

我先想了第一個方向，嘗試了幾種作法，但最後都走不下去，所以就往第二個方向，持續使用要取左半還是取右半這種判斷模式

<br/>

Binary search，對於一個已經 sort 過的 list，我們每次切中間，如果 target 更小，我們就知道接著要找左半，反之找右半
但現在因為 list 有被 rotate 過，所以要取作左半還是右半，會根據 list 是否被 rotate 過來進一判斷

所以我們要先找出有被 rotate 過的 list 的特徵
一般來說左側的值一定都小於自己，但如果一個 list 有被 rotate 過，那左側就會出現比自己大的值
所以我們就產生了一個判斷方法，看最左側的值，如果他比自己大，那就是有 roatate 過

例如：`7 8 9 0 [1] 2 3 4 5`
我們發現 7 比 1 大，所以當今天 target 是 0，他一定是存在於 1 的左半，因為左半勢必是已經列舉完所有比 1 小的值，才有可能出現比 1 大的值
但如果今天的 target 比 1 大，他就有可能存在 1 的左半或右半。進一步判斷的方式就是看 target 是不是比最左側的還 7 大，如果比 7 還大，那他就是在左半，反之是在右半

又例如：`3 4 5 [6] 7 0 1 2`，我們發現最左的 3 比 6 小，如果 target 比 6 大，我們知道他一定在右半。因為所有比 6 大的值都不可能出現在左半，如果有任何比 6 大的值出現在左半，那最左側的值不能比 6 小
若 target 比 6 小，他就有可能存在 6 的左半或右半。進一步判斷的方式就是看 target 是不是比最左側的還 3 大，如果比 3 還大，那他就是在左半，反之是在右半

同樣的邏輯，抓最右的數來判斷也可以，如果我們發現最右側的數居然比目前的值小，那我們就知道目前的數列是有 rotate 過

大概就是醬，基於這個觀察，我們把他應用在幫助我們每次決定要取左側還是去右側的這個問題，我們整理成邏輯：

如果現在 target 比目前的值小，代表我們要往值更小的未來移動，但該選左還是選右？
    選左半的條件是
        最左側出現了比目前大的值（因為這代表所有比目前值小的部分都會在左半部列舉完畢，所以為了找到 target 肯定是選左半。例如 `7 8 0 1 [2] 3 4 5 6` target 為 0）
            或者
        最左側的值比 target 還小（這代表三者目前呈現的關係由小到大為 left < target < current，所以為了找到 target 肯定是選左半。例如 `3 4 5 [6] 0 1 2` target 為 4）
    否則選右半
如果現在 target 比目前的值大，代表我們要往值更大的未來移動，但該選左還是選右？
    選右半的條件是
        最右側出現了比目前小的值（因為這代表所有比目前值大的部分都會在右半部列舉完畢，所以為了找到 target 肯定是選右半。例如 `2 3 4 [5] 6 0 1` target 為 6）
            或者
        最右側的值比 target 還大（這代表三者目前呈現的關係由小到大為 current < target < right，所以為了找到 target 肯定是選右半。例如 `7 8 0 1 [2] 3 4 5 6` target 為 5）
    否則選左半

## Source Code (Python)

``` python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        left = 0
        right = len(nums)
        while right > left:
            i = (left + right) // 2 # effectiveness same as i = left + int((right - left)/2) 
            if nums[i] == target:
                return i
            elif nums[i] > target:  # find smaller                    
                if nums[left] > nums[i] or nums[left] <= target:
                    right = i
                else:
                    left = i+1
            else:                   # find larger
                if nums[right-1] < nums[i] or nums[right-1] >= target:
                    left = i+1
                else:
                    right = i
        return -1
```
