---
title:
  - '[HackerRank] Deque-STL (Sliding Window Maximum)'
categories: software/leetcode
hide: true
summary: 給一個數列，不斷找出滑動的固定長度區間的最大值
date: 2020-08-09 07:19:40
---

## Problem

給一個數列，不斷找出滑動的固定長度區間的最大值

* [Link](https://www.hackerrank.com/challenges/deque-stl/problem)
* 等級：**Medium**
* 推薦指數：[:star::star::star::star:] Slide window. 暴力解很容易，但 O(n) 解沒做過不容易想到

> :star: 有人推薦過的題目的我才會紀錄，所以即使我覺得只有一顆星他依舊是一題有其他人推薦的題目，只是我自己不覺得需要刷
> :star::star: 代表我覺得有時間再看就好
> :star::star::star: 代表可以刷
> :star::star::star::star: 代表一定刷
> :star::star::star::star::star: 代表多刷幾次甚至把所有 solution 都弄懂

## 想法

數列長度 `N`，window 長度 `k`，暴力解就是每次都重新 traverse 整個 `k` 找出最大的，那時間複雜度就是 `O(Nk)`

要找到 `O(N)` 解，要注意到一個規則：當 slide window 一直滑，假設右端從 `x` 滑到 `x+1`，一但我們發現一個更大的值，也就是當 `a[x+1]` 比 `a[x]` 大，那 `x+1` 的左邊都是無用的，都可以丟棄，因為最新加入的 `x+1` 不但最大，並且也會是最晚離開的，所以他的左側都不會再有用處；換句話說，如果我們新發現的值 `a[x+1]` 比 `a[x]` 還小，那 `a[x+1]` 不可能是目前 slide window 的解，但卻有可能是未來 slide window 的解
根據這個特性，如果 slide window 一直滑，一但遇到比較大的就把他左側比他小的的都丟棄，就會持續維持住一個最左側就是目前 slide window 最大值的數列

例如以下數列 `a`，`k`=3

|     |   |   |   |   |   |   |   |   |   |
|-----|---|---|---|---|---|---|---|---|---|
|index| 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
|value| 10 | 30 | 20 | 10 | 40 | 50 | 20 | 30 | 60 |

我們持續維護一個 queue `q`，來記錄有用的 **index**

<br/>

首先把第一個 slide window 生出來

|     |   |
|-----|---|
|index| 0 |
|value| 10 |

`q = [0]`

<br/>

|     |   |   |
|-----|---|---|
|index| 0 | 1 |
|value| 10 | 30 |

我們新發現 `a[1]` = 30，30 比目前 `q` 中記的 index 0 的值（也就是 `a[0]`）還大，所以 0 應該被丟棄，他不會再有用，所以 `q` 就變成
`q = [1]`

<br/>

|     |   |   |   |
|-----|---|---|---|
|index| 0 | 1 | 2 |
|value| 10 | 30 | 20 |

我們新發現 `a[2]` = 20，20 比目前 `q` 中記的 index 1（也就是 `a[1]`=30）還小，代表他可能會在未來的 slide window 有用，我們需要把他記住，所以 `q` 就變成
`q = [1, 2]`

到這裡我們就看完了第一個長度為 `k` 的 slide window，我們記在 `q` 中的最左測的那個 index，就會是目前 slide window 的最大值，也就是 `a[q[0]] = a[1] = 30`

<br/>

|     |   |   |   |   |
|-----|---|---|---|---|
|index| 0 | 1 | 2 | 3 |
|value| 10 | 30 | 20 | 10 |

slide window 再往右滑一格，看到了 `a[3]` = 10，10 比 `q` 中記的最右側的 2（也就是 `a[2]`=20）還小，他可能會在未來的 slide window 有用，我們需要把他記住，所以 `q` 就變成
`q = [1, 2, 3]`

同時這代表目前滑到 index 3 的 slide window，最大值會是 index 1（看 `q` 中最左側的記憶）

<br/>

|     |   |   |   |   |   |
|-----|---|---|---|---|---|
|index| 0 | 1 | 2 | 3 | 4 |
|value| 10 | 30 | 20 | 10 | 40 |

slide window 再往右滑一格，看到了 `a[4]` = 40，40 比 `q` 中記的最右側的 3（也就是 `a[3]`=10）還大，此時 `q` 中比 40 還小的都會失去用處（因為我們的 slide window 已經滑到了一個 40 並且之後幾步 40 也都會一直存在，他會是目前的 `q` 裡最晚消失的）
所以我們要把 `q` 中從右端開始，把比 40 小的都移除：原本的 `q` = [1, 2, 3]
`a[3]`=10 比 40 小，所以把 3 移除，`q` 變成 [1, 2]
`a[2]`=20 還是比 40 小，所以把 2 移除，`q` 變成 [1]
`a[1]`=30 還是比 40 小，所以把 1 移除，`q` 變成 []

所以到了這一步，q 變成只有新發現的 4
`q = [4]`

同時也代表 `a[4]` 是目前 slide window 的最大值

<br/>

|     |   |   |   |   |   |   |
|-----|---|---|---|---|---|---|
|index| 0 | 1 | 2 | 3 | 4 | 5 |
|value| 10 | 30 | 20 | 10 | 40 | 50 |

slide window 再往右滑一格，看到了 `a[5]` = 50，50 比 `q` 中記的 4（也就是 `a[4]`=40）還大，此時 `q` 中比 50 還小的都會失去用處
所以我們要把 `q` 中從右端開始，把比 50 小的都移除：原本的 `q` = [4]，`a[4]`=40 比 50 小，所以把 4 移除，`q` 變成 []

所以到了這一步，q 變成只有新發現的 5
`q = [5]`

同時也代表 `a[5]` 是目前 slide window 的最大值

<br/>

|     |   |   |   |   |   |   |   |
|-----|---|---|---|---|---|---|---|
|index| 0 | 1 | 2 | 3 | 4 | 5 | 6 |
|value| 10 | 30 | 20 | 10 | 40 | 50 | 20 |

slide window 再往右滑一格，看到了 `a[6]` = 20，20 比 `q` 中記的 5（也就是 `a[5]`=50）還小，代表他可能在未來有用處，要記住，所以 `q` 變成
`q = [5, 6]`

同時這代表目前滑到 index 6 的 slide window，最大值依然會是 index 5（看 `q` 中最左側的記憶）

<br/>

|     |   |   |   |   |   |   |   |   |
|-----|---|---|---|---|---|---|---|---|
|index| 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 |
|value| 10 | 30 | 20 | 10 | 40 | 50 | 20 | 30 |

slide window 再往右滑一格，看到了 `a[7]` = 30，30 比 `q` 中最右側的記憶 6（也就是 `a[6]`=20）還大，此時 `q` 中比 30 還小的都會失去用處
所以我們要把 `q` 中從右端開始，把比 30 小的都移除：原本的 `q` = [5, 6]，`a[6]`=20 比 30 小，所以把 6 移除，`q` 變成 [5]
`a[5]`=50 比 30大，所以可以就留著

所以到了這一步，q 變成有剩下的 5，加上新的 7
`q = [5, 7]`

同時也代表 index 5 依舊是目前 slide window 的最大值

<br/>

|     |   |   |   |   |   |   |   |   |   |
|-----|---|---|---|---|---|---|---|---|---|
|index| 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
|value| 10 | 30 | 20 | 10 | 40 | 50 | 20 | 30 | 60 |

slide window 再往右滑一格，看到了 `a[8]` = 60，60 比 `q` 中最右側的記憶 7（也就是 `a[7]`=30）還大，此時 `q` 中比 60 還小的都會失去用處
所以我們要把 `q` 中從右端開始，把比 60 小的都移除：原本的 `q` = [5, 7]，`a[7]`=30 比 60 小，所以把 7 移除，`q` 變成 [5]

這裏要另外注意，`q` 中的 5 已經超出了我們目前 slide window 的容許範圍，他其實已經不可能再有用，所以要將其移除，所以 `q` 就變成 []

所以到了這一步，q 變成只有新加入的 8
`q = [8]`

同時也代表 `a[8]` 是目前 slide window 的最大值

## Source Code (C++)

``` cpp
#include <iostream>
#include <deque> 
using namespace std;

void printKMax(int arr[], int n, int k){
	//Write your code here.
    deque<int> q;

    for (int i=0; i<k; i++) {
        while (!q.empty() && arr[q.back()] < arr[i]) q.pop_back();
        q.push_back(i);
    }
    cout << arr[q.front()] << " ";

    for (int i=k; i<n; i++) {
        if (!q.empty() && q.front() <= i-k) q.pop_front();
        while (!q.empty() && arr[q.back()] < arr[i]) q.pop_back();
        q.push_back(i);
        cout << arr[q.front()] << " ";
    }

    cout << endl;
}

int main(){
  
	int t;
	cin >> t;
	while(t>0) {
		int n,k;
    	cin >> n >> k;
    	int i;
    	int arr[n];
    	for(i=0;i<n;i++)
      		cin >> arr[i];
    	printKMax(arr, n, k);
    	t--;
  	}
  	return 0;
}

```

時間複雜度的部分，看似 `O(Nk)` 但其實不到
因為每一輪雖然都要 traverse `q`，但 `q` 的長度並非 `k`
所以要換一個角度想，我們其實是在維護 `q`，而每一輪一個 index 會被加入 q，被加入的 index 為來有可能會被移出 q，所以總共頂多會有 2N 次的 add/pop operation，所以時間複雜度是 `O(N)`
與暴力解的 `O(Nk)` 相比，可以發現當 k>2，這會是比較快的解，代價是多一個 `q` 的 k 的空間

> [這裏](https://www.geeksforgeeks.org/sliding-window-maximum-maximum-of-all-subarrays-of-size-k/#disqus_thread) 還有提供 AVL tree 的 O(N * Log k) 解

