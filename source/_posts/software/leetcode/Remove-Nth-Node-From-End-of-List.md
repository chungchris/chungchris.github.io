---
title:
  - '[LeetCode] #19 Remove Nth Node From End of List'
tags:
  - leetcode:ll
categories: software/leetcode
keywords:
  - linked list
hide: true
summary: 移除一個單向 linked list 中倒數的 node
date: 2020-07-09 09:08:28
---

## Problem

移除一個單向 linked list 中倒數的 node

* [Link](https://leetcode.com/problems/remove-nth-node-from-end-of-list/)
* 等級：**Medium**
* 推薦指數：[:star::star::star:] 暴力解很容易，進階解不一定想到到，但看過就會。如果對 Rust 有興趣的可以就本題了解一些 Rust 的限制

> :star: 有人推薦過的題目的我才會紀錄，所以即使我覺得只有一顆星他依舊是一題有其他人推薦的題目，只是我自己不覺得需要刷
> :star::star: 代表我覺得有時間再看就好
> :star::star::star: 代表可以刷
> :star::star::star::star: 代表一定刷
> :star::star::star::star::star: 代表多刷幾次甚至把所有 solution 都弄懂

---

## 想法

既然要倒數，那肯定要知道長度（或者說是知道尾巴），這無法省
所以關鍵變成是能不能在一知道長度的當下，就知道要刪除哪個節點，這樣才會最快。如果知道了長度之後還要再做一次 iterate 才能刪除某個節點，那可能慢了

> 當然我們不提把一個單向的 linked list 擴充成雙向或者有 index 的這種做法，這樣 space double 應用在本題算是單純的情境有些小題大作

有了這個想法之後其實離解題就不遠
我們要維護一個 pointer，不斷指向最有可能、或者說最接近目標刪除節點節點。我們的目標刪除節點是誰？就是距離尾巴 n 的節點，也就是說我們從 head 開始跑，每多跑到一個點，我們都當作他是尾巴，把 pointer 不斷維持在距離這個尾巴 n 的位置
如此當我們真的走到尾巴，pointer 指的的那個節點就是要刪除的點

當然還有兩個小轉折：第一個是，我們其實要維護 pointer 指向的其實不是要刪除的那個節點，而應該是要刪除的那個節點的前一個節點，這樣才有辦法做刪除。因此也就有了第二個小轉折，我們可以在頭部新增一個 dummy 的節點，用來處理如果要刪除的節點是就 head 的狀況；這不是必要，但有的話會比較好思考而且 code 好看一些

> 因為本題的 input 限制有說不會給 len 為 0 的數列，也不會給超出範圍的 n，所以其他 corner case 就不用考慮了

## Source Code (Python)

``` python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def removeNthFromEnd(self, head: ListNode, n: int) -> ListNode:
        D = ListNode(None, head)

        p = D
        node = head
        while node != None:
            if n == 0:
                p = p.next
            else:
                n = n - 1 # n 的運用就可以不用另外維護一個用來數 list 長度的變數
            node = node.next

        if p == D: # 刪除 head 節點的狀況
            head = head.next
        else:
            p.next = p.next.next

        return head
```

## Source Code (Go)

``` go
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */
func removeNthFromEnd(head *ListNode, n int) *ListNode {
    D := ListNode{ Val:0, Next: head}
    
    p := &D
    node := head
    for node != nil {
        if n == 0 {
            p = p.Next
        } else {
            n--
        }
        node = node.Next
    }
    
    if *p == D {
        head = head.Next
    } else {
        p.Next = p.Next.Next
    }
    
    return head
}
```

## Source Code (Rust)

上面 Python 和 Go 的寫法可以叫做 one-pass solution 因為只有一個 loop
但其實該 loop 裡面同時做兩件事，一個是倒數 `n`，一個是移動 `p` 和 `node` 兩個 pointer

如果我們改成先移動 node 從頭移到尾，因此的知了整個 list 長度，減去 `n` 就知道要刪除的是第幾個節點，然後再從頭跑一次。因為這樣會有兩個 iteration，所以叫做 two-pass solution
two-pass solution 是要兩個 loop 沒錯，但首先，這兩個 loop 並不是 nest，所以時間複雜度還是算 `O(n)`；而且，從移動 pointer 的『次數』的角度來看，one-pass 和 two-pass 其實是一樣的，因為在 one-pass solution 中，`p` 被移動 (len - n) 次，`node` 被移動 len 次

但為什麼現在要把原本比較間簡潔的 one-pass 改成 two-pass？因為 one-pass 如果要用 Rust 實作是做不出來的（除非用 unsafe）
注意到 one-pass 之所以可以在同一個 loop 裡處理，就是因為同一個 loop 裡同時操作了兩個 pointer- `node` 和 `p`
而這個是會違反 Rust 的 borrow checker 的：Rust 的 borrow checker 禁止同時存在兩個 mutable reference，也禁止一個變數同時被 mutable/unmutable borrowed

這樣不行因為 `p1` 和 `p2` 的生命有重疊且同時為 mutable reference
``` rust
fn main() {
    let mut a = 1;
    let mut p1 = &mut a;
    let mut p2 = &mut a;
    println!("{}", p1);
    println!("{}", p2);
}
```

這樣不行因為 `p1` 和 `p2` 的生命有重疊且一個為 mutable 一個為 unmutable
``` rust
fn main() {
    let mut a = 1;
    let mut p1 = &mut a;
    let mut p2 = & a;
    println!("{}", p1);
    println!("{}", p2);
}
```

所以在這一題我們無法同時有兩個 reference- `p` 和 `node` 指向的都是同一個 linked list 串列
你可能會說實際上 `p` 和 `node` 可能沒有同時指到 linked list 中的同一個節點啊，為什麼這樣還不能過？但問題就是 compiler 不會知道 runtime 時的事情的，只要你是透過相同變數去 access 這個 linked list（比如透過 head），那就是會被 borrow checker 湊在一起檢查，borrow checker 只看 identifier 的 liveness scope

如果無法體會，那你嘗試自己用 Rust implement 一次 one-pass solution 就會知道了... 怎麼修都修不過

所以在 Rust 我們就要改成 two-pass 作法，先得知 list 長度 len，再從頭來過移動 (len - n) 步

``` rust
// Definition for singly-linked list.
// #[derive(PartialEq, Eq, Clone, Debug)]
// pub struct ListNode {
//   pub val: i32,
//   pub next: Option<Box<ListNode>>
// }
// 
// impl ListNode {
//   #[inline]
//   fn new(val: i32) -> Self {
//     ListNode {
//       next: None,
//       val
//     }
//   }
// }
impl Solution {
    pub fn remove_nth_from_end(mut head: Option<Box<ListNode>>, mut n: i32) -> Option<Box<ListNode>> {
        let mut len = 0;
        {
            let mut p = &head;
            loop {
                if p.is_none() {
                    break;
                }
                len = len + 1;
                p = &(p.as_ref().unwrap().next);
            }
        }

        if len == n { // corner case that the head node is the one to be removed
            return head.unwrap().next;
        }

        len = len - n - 1;
        {
            let mut t = &mut head;
            for _ in (0..len) {
                t = &mut (t.as_mut().unwrap().next);
            }
            let n = t.as_mut().unwrap(); // n is &ListNode
            n.next = n.next.as_mut().unwrap().next.take();
            //       n is &ListNode
            //        .next is Option<Box<ListNode>> because n is auto deref
            //             .as_mut() is Option<&Box<ListNode>>
            //                      .unwrap() is &Box<ListNode>. We can do unwrap because we know it's impossible being None here.
            //                               .next is Option<Box<ListNode>>
            //                                    .take() is necessary because we want to take ownership of Option<Box<ListNode>> and move it into `n.next`
        }
        
        head
    }
}
```

## Source Code (C)

``` c
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     struct ListNode *next;
 * };
 */

struct ListNode* removeNthFromEnd(struct ListNode* head, int n){
    struct ListNode D = { 0, head };
    struct ListNode *p = &D;
    struct ListNode *c = head;
    while (c != NULL) {
        if (n == 0) {
            p = p->next;
        } else {
            n -= 1;
        }
        c = c->next;
    }
    
    if (p == &D) {
        head = head->next;
    } else {
        p->next = p->next->next;
    }
    
    return head;
}
```

> 最後也來看一下 4 種語言的 performance
>
> | Runtime | Memory | Language |
> |-|-|-|
> | 28 ms | 14.3 MB | python3 |
> | 0 ms | 2.3 MB | golang |
> | 0 ms | 2 MB | rust |
> | 4 ms | 6 MB | c |
