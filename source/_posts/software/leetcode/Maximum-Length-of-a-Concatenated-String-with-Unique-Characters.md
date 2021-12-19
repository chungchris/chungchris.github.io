---
title:
  - '[LeetCode] #1239 Maximum Length of a Concatenated String with Unique Characters'
categories: software/leetcode
hide: true
summary: 提供一些字串，用其組合出不重複字元的字串，找出最大的可能長度
date: 2020-08-27 11:13:39
---

## Problem

提供一些字串，用其組合出不重複字元的字串，找出最大的可能長度

* [Link](https://leetcode.com/problems/maximum-length-of-a-concatenated-string-with-unique-characters/)
* 等級：**Medium**
* 推薦指數：[:star::star::star::star:] 兩個關鍵，第一個是要會由一個給定的 list 去列出所有的無序組合，第二個是透過 bit 來取代 map 做記憶可以節省記憶體

> :star: 有人推薦過的題目的我才會紀錄，所以即使我覺得只有一顆星他依舊是一題有其他人推薦的題目，只是我自己不覺得需要刷
> :star::star: 代表我覺得有時間再看就好
> :star::star::star: 代表可以刷
> :star::star::star::star: 代表一定刷
> :star::star::star::star::star: 代表多刷幾次甚至把所有 solution 都弄懂

## 想法

題目給一群字串，要用其中任意個，去儘可能的組出最長的沒有重複字元的字串
題目給的字串只有小寫字母，也就是說我們能組出的答案頂多長度就是 26，意識到這件事就為後續我們使用 bitset 來記憶提供了一個提示

我們需要記憶每個 char 的出現，才得以去比較兩個 string 是否有出現相同的 char
直觀上我們用 set 或者 map，去走過兩個 string，看看會不會有重複的 add
但既然我們今天已經知道所有的可能就是 26 個，那用一個長度為 26-bit 的空間，就可以更有效率，不但記憶的空間減少，比較的動作速度也有可能提升
比如 `"abc"` 就是 `[11100000000000000000000000]`

<br/>

另外是排列組合的部分
本題不關心順序，也就是沒有排列問題，只有組合問題
我們知道數學上來說，給 `n` 個元素，就是有 `2^n` 個組合：這來自於一個直觀想法就是『取』或『不取』，對每一個元素都有『取』或『不取』兩種選擇，所以總共有 `2^n` 種可能

但是如果我們要印出所有組合，他的演算法就會是 greedy 的隨著 traverse list 而擴展。比如現在給一個 list `[A, B, C]`
我們先走到 A，所以就產生了 `[A]` 這個可能組合
然後走到 B，除了 B 本身是一個可能組合外，B 和已知組合的組合也會是可能組合，所以就變成 `[A, B, AB]`
然後走到 C，除了 C 本身是一個可能組合外，C 和已知組合的組合也會是可能組合，所以就變成 `[A, B, AB, C, AC, BC, ABC]`
（再加上什麼都不取的 case 就有 8 種，也就是等於 `2^3`）

有了以上兩個關鍵想法之後就可以實作出來了

## C++

``` cpp
class Solution {
public:
    int maxLength(vector<string>& v) {
        int ans = 0;
        vector<bitset<26>> combinations;

        for (auto& s: v) {
            bitset<26> id;
            for (auto& c: s) {
                id.set(c-'a');
            }
            
            int count = id.count();
            if (count != s.size()) { continue; }
            
            combinations.push_back(id);
            ans = count > ans ? count : ans;
            
            for (int i=0; i<combinations.size()-1; i++) {
                if ((combinations[i] & id) != 0) {
                    continue;
                }
                
                combinations.push_back(id | combinations[i]);
                ans = max<int>(ans, combinations[i].count() + count);
            }
        }
        
        return ans;
    }
};
```
