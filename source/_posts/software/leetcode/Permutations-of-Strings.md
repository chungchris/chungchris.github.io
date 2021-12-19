---
title:
  - '[HackerRank] Permutations of Strings'
categories: software/leetcode
hide: true
summary: 產生所有排列組合
date: 2020-07-31 18:37:11
---

## Problem

給定一些 strings，產生所有不同順序的排列組合，且依據 lexicographical order 輸出

* [Link](https://www.hackerrank.com/challenges/permutations-of-strings/problem)
* 等級：**Medium**
* 推薦指數：[:star::star::star::star:] 產生所有排列組合的其中一種演算法，有自動跳掉相同外表的排列的效果

> :star: 有人推薦過的題目的我才會紀錄，所以即使我覺得只有一顆星他依舊是一題有其他人推薦的題目，只是我自己不覺得需要刷
> :star::star: 代表我覺得有時間再看就好
> :star::star::star: 代表可以刷
> :star::star::star::star: 代表一定刷
> :star::star::star::star::star: 代表多刷幾次甚至把所有 solution 都弄懂

## 想法

演算法要描述在[這裡](https://en.wikipedia.org/wiki/Permutation#Generation_in_lexicographic_order)

思路上我們可以直接隨便寫個例子，再來觀察我們自己是怎麼找出所有排列組合

比如現在給一個數列 [1, 2, 3, 4, 5]
他的下一個排列組合為 [1, 2, 3, 5, 4]
他的下一個排列組合為 [1, 2, 4, 3, 5]
他的下一個排列組合為 [1, 2, 4, 5, 3]
他的下一個排列組合為 [1, 2, 5, 3, 4]
他的下一個排列組合為 [1, 2, 5, 4, 3]
他的下一個排列組合為 [1, 3, 2, 4, 5]
他的下一個排列組合為 [1, 3, 2, 5, 4]
他的下一個排列組合為 [1, 3, 4, 2, 5]
他的下一個排列組合為 [1, 3, 4, 5, 2]
他的下一個排列組合為 [1, 3, 5, 2, 4]
...

我們其時很容易可以寫出來答案，但要整理成邏輯反而有點困難

1. 找到最大可能的 index `k` 使 `a[k] < a[k+1]`。如果找不到，代表已窮盡所有排列組合
    * 因為整個數列一定已經變成從大排到小，才會找不到任何 index `k` 使得 `a[k] < a[k+1]`
2. 在 `k` 的右側，找到最大可能的 index `m`，使得 `a[m] > a[k]`，把 `a[m]` `a[k]` 位置互換
    * 當我們在步驟 1 中找到 `k`，同時代表 `k` 的右側數列已經是由大到小排序完畢
    * 所以本步驟要找到最大可能的 index `m`，使得 `a[m] > a[k]`，其實就是從左往右找，找到第一個大於 a[k] 的即是 `m`
    * 把 `a[m]` `a[k]` 位置互換，`k` 的右側數列依然會維持大到小排序
3. 把 `k` 右側的數列反向排序
    * 這樣正會得到以 `k` 為開頭的數列的最小 order 的排列。因為在步驟 2 後，`k` 的右側數列是大到小排序，所以 reverse 後就是由小到大

## Source Code (C)

要注意到本題 **Given an array of strings sorted in lexicographical order, print all of its permutations in strict lexicographical order.**，代表他的 input 本身已經是排序好的

``` c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int next_permutation(int n, char **s)
{
	/**
	* Complete this method
	* Return 0 when there is no next permutation and 1 otherwise
	* Modify array s to its next permutation
	*/
    int k = n-2;
    while (k >= 0) {
        if (strcmp(s[k], s[k+1]) < 0) {
            break;
        }
        k--;
    }

    if (k < 0) {
        return 0;
    }

    int m = n-1;
    while (m > k) {
        if (strcmp(s[m], s[k]) > 0) {
            break;
        }
        m--;
    }
    
    char *temp = s[k];
    s[k] = s[m];
    s[m] = temp;
    
    int i = k+1;
    int j = n-1;
    while (i < j) {
        temp = s[i];
        s[i] = s[j];
        s[j] = temp;
        i++;
        j--;
    }
    
    return 1;
}

int main()
{
	char **s;
	int n;
	scanf("%d", &n);
	s = calloc(n, sizeof(char*));
	for (int i = 0; i < n; i++)
	{
		s[i] = calloc(11, sizeof(char));
		scanf("%s", s[i]);
	}
	do
	{
		for (int i = 0; i < n; i++)
			printf("%s%c", s[i], i == n - 1 ? '\n' : ' ');
	} while (next_permutation(n, s));
	for (int i = 0; i < n; i++)
		free(s[i]);
	free(s);
	return 0;
}
```
