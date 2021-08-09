---
title:
  - '[LeetCode] #49 Group Anagrams'
tags:
  - leecode:string
categories: software/leetcode
hide: true
summary: 找字元組成相同但順序不同的字串（anagrams）
date: 2020-07-13 08:17:35
---

## Problem

找字元組成相同但順序不同的字串. a.k.a anagrams

* [Link](https://leetcode.com/problems/group-anagrams/)
* 等級：**Medium**
* 推薦指數：[:star::star:] 土法煉鋼，沒什麼高深想法，稍有練習不同語言基本語法的作用

> :star: 有人推薦過的題目的我才會紀錄，所以即使我覺得只有一顆星他依舊是一題有其他人推薦的題目，只是我自己不覺得需要刷
> :star::star: 代表我覺得有時間再看就好
> :star::star::star: 代表可以刷
> :star::star::star::star: 代表一定刷
> :star::star::star::star::star: 代表多刷幾次甚至把所有 solution 都弄懂

## 想法

字元組成相同，代表每一個 char 使用的次數相同，比如 a 都出現 2 次，b 都出現 1 次，c 都出現 3 次，那不管順序怎麼換，都是為同一組
所以問題就轉變為替每一種樣式創造一個 unique 的 identifier，比如 `a2b1c3d0e0f0...`

# Source Code (Python)

``` python
class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        base = ord('a')
        
        collect = {} # { str: [str, str, ...], str: [str, str, ...] }
        for s in strs: # O(N)
            count = [0]*26
            for c in s: # O(K)
                count[ord(c)-base] += 1

            # list is not hashable, dict is not hashable
            '''id = ""
            for n in count: # O(26)
                id += (str(n)+",")'''
            id = tuple(count) # tuple is hashable. 這個轉換會比上面自己轉成 string 快一些


            if id in collect:
                collect[id].append(s)
            else:
                collect[id] = [s]

        return collect.values()
```

時間複雜度是 `O(NK)`，`N=len(strs)`，`K` 是每個 `strs` 中每個 string 的(平均/最大)長度

## Source Code (Go)

``` go
func groupAnagrams(strs []string) [][]string {
    base := int('a')

    collect := make(map[[26]int][]string) // map { array [26]int: slice [strings] }.
    //  - array 可以作為 map 的 index
    //  - make() will do initialize. not zeroed, but still initialized)
    for _,s := range strs {
        count := [26]int{} // array
        for _,c := range s {
            count[int(c)-base]++
        }
        collect[count] = append(collect[count], s) // so we can directly do appen here
    }
    
    ans := make([][]string, 0)
    for _,v := range collect {
        ans = append(ans, v)
    }
    
    return ans
}
```

## Source Code (Rust)

``` rust
impl Solution {
    pub fn group_anagrams(strs: Vec<String>) -> Vec<Vec<String>> {
        use std::collections::HashMap;
        let base = 'a' as u32;
        
        let mut collect: HashMap<String, Vec<String>> = HashMap::new();
        for s in strs {
            let mut count: [i32; 26] = [0; 26];
            for c in s.chars() {
                count[(c as u32 - base) as usize] += 1;
            }
            
            let id = format!("{:?}", count); // rust 無法直接以 array 作為 hashmap，除非實作 Eq 和 Hash traits
            match collect.get_mut(&id) {
                Some(r) => { r.push(s); }, // r is auto deref
                None => { collect.insert(id, vec![s]); }
            }
        }
        
        let mut ans: Vec<Vec<String>> = vec![];
        for (_,v) in collect.drain() { // remove from the collect since we no longer need
            ans.push(v);
        }
        
        ans
    }
}
```

## Source Code (C)

``` c
#define MAX_GROUP 16

char* itoa(int val, int base){
	static char buf[32] = {0};
	int i = 30;
	for(; val && i ; --i, val /= base)
		buf[i] = "0123456789abcdef"[val % base];
	return &buf[i+1];
}

typedef struct my_struct {
    char* id;                    /* key */
    int members[MAX_GROUP];
    int count;
    UT_hash_handle hh;         /* makes this structure hashable */
}HT;

/**
 * Return an array of arrays of size *returnSize.
 * The sizes of the arrays are returned as *returnColumnSizes array.
 * Note: Both returned array and *columnSizes array must be malloced, assume caller calls free().
 */
char *** groupAnagrams(char ** strs, int strsSize, int* returnSize, int** returnColumnSizes) {
    HT *ht = NULL;

    for (int i=0; i<strsSize; i++) {
        char *s = strs[i];
        int collect[26] = { 0 };
        for (int j=0; j<strlen(s); j++) {
            collect[s[j] - 'a'] += 1;
        }

        int id_len = 128;
        char *_id = (char*)malloc(id_len);
        memset(_id, 0, id_len);
        for (int j=0; j<26; j++) {
            char* buffer = itoa(collect[j], 10);
            if (strlen(_id) + strlen(buffer) + 1 >= id_len) {
                id_len *= 2;
                _id = (char*)realloc(_id, id_len);
            }
            strncat(_id, buffer, strlen(buffer));
            if (j != 25) {
                strncat(_id, ",", 1);
            }
        }
        //printf("%s:%s\n", s, _id);

        HT *r = NULL;
        HASH_FIND_STR(ht, _id, r);
        if (r == NULL) {
            r = (HT*)malloc(sizeof(HT));
            r->id = _id;
            r->members[0] = i;
            r->count = 1;
            HASH_ADD_KEYPTR(hh, ht, r->id, strlen(r->id), r);
            //printf("added %s", s);
        } else {
            if (r->count == 32) exit(1);
            r->members[r->count] = i;
            r->count++;
            //printf("appended %s\n", s);
        }
    }

    *returnSize = HASH_COUNT(ht);
    if (*returnSize == 0) {
        return NULL;
    }

    *returnColumnSizes = (int*)malloc((*returnSize) * sizeof(int));
    memset(*returnColumnSizes, 0, (*returnSize) * sizeof(int));

    char*** ret = (char***)malloc(sizeof(char**) * (*returnSize));
    memset(ret, 0, sizeof(char**) * (*returnSize));

    HT *r = ht;
    for (int i=0; r != NULL; i++) {
        (*returnColumnSizes)[i] = r->count;
        ret[i] = (char**)malloc(sizeof(char*) * (r->count));

        for (int j = 0; j < r->count; j++) {
            ret[i][j] = strs[r->members[j]];
        }

        r = (HT*)(r->hh.next);
    }

    return ret;
}
```

> 最後也來看一下 4 種語言的 performance
>
> | Runtime | Memory | Language |
> |-|-|-|
> | 116 ms | 19.8 MB | python3 |
> | 20 ms | 9 MB | golang |
> | 20 ms | 5.1 MB | rust |
> | 60 ms | 19.2 MB | c |
