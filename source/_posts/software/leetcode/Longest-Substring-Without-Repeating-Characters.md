---
title: '[LeetCode] #3 Longest Substring Without Repeating Characters'
categories: software/leetcode
hide: true
summary: 找一個給定 string 當中，不出現重複 char 的 substring 的最大可能長度
date: 2020-07-07 17:43:38
---

## Problem

找一個給定 string 當中，不出現重複 char 的 substring 的最大可能長度

* [Link](https://leetcode.com/problems/longest-substring-without-repeating-characters/)
* 等級：**Medium**
* 推薦指數：[:star::star::star:] 可與 [#340 Longest Substring with At Most K Distinct Characters](https://www.cnblogs.com/grandyang/p/5351347.html) 對照。因為本題是要求完全不重複，所以我們的 map 是記 char 出現過的 index，這讓我再發現重複的時候，可以直接調整 window 的 head 到新的位置；但 340 是容許 k 次重複，所以若我們的 map 改記得的是 char 出現的次數，那每當重複次數超過容許值因而要調整 head，就要一步一步調，當然如果 map 是把每個 char 出現的位置都記錄下來，自然也就可以一次調到位。有就是說 340 其實比較有 slide window 的感覺，但本題其實不需要明確有 window 的概念，而是比較像維護一個 valid head 的 index 就可以

> :star: 有人推薦過的題目的我才會紀錄，所以即使我覺得只有一顆星他依舊是一題有其他人推薦的題目，只是我自己不覺得需要刷
> :star::star: 代表我覺得有時間再看就好
> :star::star::star: 代表可以刷
> :star::star::star::star: 代表一定刷
> :star::star::star::star::star: 代表多刷幾次甚至把所有 solution 都弄懂

## 想法

string 是一定要從頭掃的，但為了節省時間，儘量不要有掃過的地方又重疊掃一次的狀況，看要記住什麼可以避免未來再掃一次就把應該把該資訊記住

|       |   |   |   |   |   |   |   |   |   |   |   |
|-------|---|---|---|---|---|---|---|---|---|---|---|
| index | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 |...|
| char  | a | b | c | d | e | f | c | g | h | e |...|

1. 從 index 0 開始往右走，遇到過什麼 char 就把他記住，記住的方法用 HashTable，因為 HashTable 的未來取用基本上可以當作 `O(1)`
2. 如果一直沒掃到曾經出現過的就一直往右掃，直到掃到 index 6 的時候發現了第一個重複的 char `c`，我們的 HashTable 記得 `c` 上次出現在 index 2
3. 這時候會產生兩個**有重疊的** substring 有可能是最終解，第一個是從 `s[0:6]`，第二個是 `s[3:end]`，所以目前我們就可以先將答案更新為 `s[0:6]` 的長度 6
4. 接下來的問題就是，我們會不會在 s[3:end] 發現一個比 6 更好的答案？所以從現在開始 s[3:end] 就是我們面對的新 string。
    (1) 在這個新 string 中，`c` 出現的點是 index 6，所以要更新 HashTable 的記憶
    (2) 如果原本 HashTable 的記憶中，有任何 char 出現的點是在 index 3 以前的，我們現在都要當作沒看過（也就是要從 HashTable 中刪除），因為他們已經不在我們現在關注的 string 範圍內
5. 重複上述步驟一路走到整個 string 結束

有了基本的想法後就可以開始寫 code

> 最後記得檢視一些 edge case 看能不能 cover
>    a. 當答案的 substring 存在整個 string 的尾巴
>    b. 如果從頭到尾都沒有重複的 char
>    c. 如果 string 長度為 0
>    d. 如果 string 長度為 1
>    e. 如果 string 只存在同一個 char 一直重複

## Source Code (Python)

``` python
class Solution:
    '''
    * without repeating => we need to remember what we have seen
    * longest => only need a number, instead of the exact string
    * time cost => try to traverse only once

    Example: "abcabcbb"
    
    Do it as a human:
        abcabcbb
        a => a
         b => ab
          c => abc
           a => bca
            b => cab
             c => abc
              b => cb
               b => b
    
        abcabcbb
        a => {a:0} valid string starts at index 0
         b => {a:0, b:1}
          c => {a:0, b:1, c:2}
           a => seen `a` before at index 0, now we are seeing `a` again at index 3
                it leads to 2 substrings: the first one is from 0 to 3-1, its length is 3; the second one is from 0+1 to afterward
                update the memory {a:3, b:1, c:2}
            b => seen `b` before at index 1, now we are seeing `b` again at index 4
                 it leads to 2 substrings: the first one is from 1 to 4-1, its length is 3; the second one is from 1+1 to afterward
                 update the memory {a:3, b:4, c:2}
             c => seen `c` before at index 2, now we are seeing `c` again at index 5
                  it leads to 2 substrings: the first one is from 2 to 5-1, its length is 3; the second one is from 2+1 to afterward
                  update the memory {a:3, b:4, c:5}
              b => seen `b` before at index 4, now we are seeing `b` again at index 6
                  it leads to 2 substrings: the first one is from 3 to 6-1, its length is 3; the second one is from 4+1 to afterward
                  update the memory {a:3, b:6, c:5}
               b => seen `b` before at index 6, now we are seeing `b` again at index 7
                  it leads to 2 substrings: the first one is from 5 to 7-1, its length is 2; the second one is from 6+1 to afterward
                EOS => there is a last valide string, it's head is at index 7, so its length is 1
    
    Test cases:
      answer locates at head part: abccc
      answer locates at tail part: aaabc
      all the same: aaa
      all different: abc
      empty: ""
      single: a
    '''
    def lengthOfLongestSubstring(self, s: str) -> int:
        d = {} # { char: int } memorize the index of last time specific char showing up
        p = 0  # save the head of the valid unchecked string
        ans = 0

        for i in range(len(s)):
            if s[i] in d \
                    and d[s[i]] >= p:
                # if the char has been found before, but it's located in don't care part, take it as first time seen.
                l = i - p
                ans = l if l > ans else ans
                p = d[s[i]] + 1
            d[s[i]] = i

        # handle the tail case. i.e. edge case (a), (b)
        l = len(s) - p
        ans = l if l>ans else ans

        return ans
```

* `d` 用來記憶目前看過的 char 的最後出現位置
* `p` 用來標記尚未取得最佳解的 substring 的起點，一開始整個 string 都尚未取得最佳解，所以初始值是 0。**`p` 意義，其運用和更新就是本題的關鍵**
* 其中有一個優化是對於 `想法` `步驟 4-(2)` 中要刪除不再關注的部分的記憶的做法，這直覺上會在第 15 行更新 `p` 時，也去把 `d` 當中該刪的東西刪掉。但這樣就會變成要跑一個 `d.items()` 的 for loop。所以把他優化成在第 10 行，當有必要時再進行忽略更新即可
* 時間複雜度為 `O(n)` 因為只有第 7 行的 for loop

## Source Code (Go)

``` go
func lengthOfLongestSubstring(s string) int {
    ans := 0
    p := 0
    d := make(map[rune]int) // Go 沒有 char，有的是 byte 和 rune. rune is synonym of int32 and also represent Unicode code points
    
    for i,c := range s { // Go 對於 `array`, `slice`, `string`, `map`, `channel` 都是用這樣 iterate
        x, exist := d[c]
        if exist && x >= p {
            l := i - p
            if l > ans { // Go 沒有三元運算符
                ans = l
            }
            p = x + 1
        }
        d[c] = i
    }

    l := len(s)
    if l-p > 0 && l-p > ans {
        ans = l-p
    }

    return ans
}
```

## Source Code (Rust)

``` rust
impl Solution {
    pub fn length_of_longest_substring(s: String) -> i32 {
        use std::collections::HashMap;

        let mut ans = 0;
        let mut p: i32 = 0;
        let mut d = HashMap::new();
        for (i, c) in s.chars().enumerate() { // rust 對於有實作 Iterator trait 的物件可以用 for ... in ... 來 iterate
            match d.get(&c) {
                Some(x) => {
                    if *x as i32 >= p {
                        ans = if (i as i32 - p) > ans { i as i32 - p } else { ans }; // rust 是強型別語言
                        p = *x as i32 + 1;
                    }
                },
                None => {}
            }
            d.insert(c, i);
        }
        
        ans = if (s.len() as i32 - p) > ans { s.len() as i32 - p } else { ans };
        
        ans
    }
}
```

## Source Code (C)

``` c
typedef struct _ht {
    int key;
    int value;
    UT_hash_handle hh;   /* makes this structure hashable */
} HT;

int lengthOfLongestSubstring(char * s){
    int len = strlen(s);
    HT *d = NULL;
    int ans = 0;
    int p = 0;

    for (int i=0; i<len; i++) {
        HT *t = NULL;
        int k = (int)s[i];
        HASH_FIND_INT(d, &k, t);
        if (t != NULL) {
            if (t->value >= p) {
                if ((i-p) > ans) {
                    ans = (i-p);
                }
                p = t->value + 1;
            }
        } else {
            t = (HT*)malloc(sizeof(HT));
            t->key = (int)s[i];
            HASH_ADD_INT(d, key, t);
        }
        t->value = i;
    }
    
    if ((len-p) > ans) {
        ans = len-p;
    }
    
    return ans;
}
```

上面是以 [uthash](https://troydhanson.github.io/uthash/userguide.html) 來實作 hash table 紀錄看過的 character，所以會比較耗記憶體
如果對於題目的這個 constraint- "s consists of English letters, digits, symbols and spaces"，我們知道他代表少於 128 的可能性的話（ASCII 就是 0~127），可以直接用一個 size 128 的 array 來當 hash table 就好

``` c
int lengthOfLongestSubstring(char * s){
    char* d[128] = { NULL }; // preknowledge 128 is enough for given constraint "s consists of English letters, digits, symbols and spaces"
    int ans = 0;
    char *c = s; // will iterate the s
    char *p = s; // valid window
    while (*c) {
        if (d[*c] >= p) { // char can be considered as integer directly for indexing. No problem even if d[*c] is NULL. 
            if (c-p > ans) {
                ans = c-p;
            }
            p = d[*c] + 1;
        }
        d[*c] = c;
        c++;
    }

    if (c-p > ans) {
        ans = c-p;
    }

    return ans;
}
```

## Source Code (C++)

``` cpp
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        int ans=0, p=0;
        unordered_map<int, int> m;
        for (int i=0; i<s.size(); i++) {
            if (m.find(s[i]) == m.end()) m[s[i]] = i;
            else {
                int t = m[s[i]];
                if (t >= p) {
                    if (i-p > ans) ans = i-p;
                    p = t+1;
                }
            }
            m[s[i]] = i;
        }
        
        if (s.size() - p > ans) ans = s.size() - p;

        return ans;
    }
};
```

> 最後也可以看一下 5 種語言的 performance
>
> | Runtime | Memory | Language |
> |-|-|-|
> | 52 ms | 14.4 MB | python3 |
> | 4 ms | 3.2 MB | golang |
> | 4 ms | 2.1 MB | rust |
> | 4 ms | 8.9 MB | c |
> | 0 ms | 5.8 MB | c |
> | 12 ms | 8.3 MB | c++ |
>
> * python 是 runtime 直譯器語言，且語法也很高階，所以非常慢
> * rust 比 go 更稍底層一些，理論上會比 go 更快。但我猜因為這一題 string iteration 正好是 rust performance 比較差的部分，因為 rust 的 string 是由可變長度的 unicode 組成，所以 string 的 indexing 不是 O(1)
> * 以記憶體來說，Rust 的確是最強這是符合預期的。但若以語法的角度來看，go 寫起來是比 rust 快的
> * c 就是要一切靠自己了... 4ms 是採用 uthash 作為 hash table 實作，若改為簡單用 size 128 array 就是 0ms
> * 所以可以看到 golang 和 rust 提供的 hash table 其實是蠻不錯的，當然如果把他們也都換成用 size 128 array 來當 hash table 速度也會再提升啦
