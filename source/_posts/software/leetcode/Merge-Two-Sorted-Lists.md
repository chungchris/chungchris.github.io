---
title:
  - '[LeetCode] #21 Merge Two Sorted Lists'
categories: software/leetcode
keywords:
  - linked list
  - sort
hide: true
summary: 融合兩個排序過的單向串列
date: 2020-07-11 12:22:50
---

## Problem

融合兩個排序過的單向串列

* [Link](https://leetcode.com/problems/merge-two-sorted-lists/)
* 等級：**Easy**
* 推薦指數：[:star::star::star::star::star:] 很容易，但 Python 的 List 太強大，用久了可能會忘了在有限制的單向串列狀況下怎麼處理這個問題。同時這也是一題可以觀察 Rust 語言特性的題目

> :star: 有人推薦過的題目的我才會紀錄，所以即使我覺得只有一顆星他依舊是一題有其他人推薦的題目，只是我自己不覺得需要刷
> :star::star: 代表我覺得有時間再看就好
> :star::star::star: 代表可以刷
> :star::star::star::star: 代表一定刷
> :star::star::star::star::star: 代表多刷幾次甚至把所有 solution 都弄懂

## 想法

就是從兩個串列的頭一個一個挑比較小的那個

## Source Code (Python)

``` python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def mergeTwoLists(self, l1: ListNode, l2: ListNode) -> ListNode:
        D = ListNode(None, None) # 常用技巧：創一個 dummy 的點當作 head

        cur = D # python 沒有明確 pointer 的感覺，要自己想清楚這個 cur 是什麼意思
        while l1 and l2:
            if l1.val < l2.val:
                cur.next = l1
                l1 = l1.next
            else:
                cur.next = l2
                l2 = l2.next
            cur = cur.next

        if l1:
            cur.next = l1
        if l2:
            cur.next = l2

        return D.next
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
func mergeTwoLists(l1 *ListNode, l2 *ListNode) *ListNode {
    D := ListNode { Val: 0, Next: nil }

    cur := &D
    for l1 != nil && l2 != nil {
        if l1.Val <= l2.Val { // auto deref. we are not using (*l1).Val
            cur.Next = l1
            l1 = l1.Next
        } else {
            cur.Next = l2
            l2 = l2.Next
        }
        cur = cur.Next
    }

    if l1 != nil {
        cur.Next = l1
    } else if l2 != nil {
        cur.Next = l2
    }

    return D.Next
}
```

## Source Code (Rust)

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
    pub fn merge_two_lists(mut l1: Option<Box<ListNode>>, mut l2: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        let mut D = Some(Box::new(ListNode::new(0)));
        
        let mut cur = &mut D;
        loop {
            if l1.is_none() || l2.is_none() {
                break;
            }
            if l1.as_ref().unwrap().val <= l2.as_ref().unwrap().val {
                let mut t = l1.unwrap(); // move out
                l1 = t.next.take(); // cut its next, move to l1
                cur.as_mut().unwrap().next = Some(t); // append the remaining to cur
            } else {
                let mut t = l2.unwrap();
                l2 = t.next.take();
                cur.as_mut().unwrap().next = Some(t);
            }
            cur = &mut (cur.as_mut().unwrap().next);
        }

        if l1.is_some() {
            cur.as_mut().unwrap().next = l1;
        } else if l2.is_some() {
            cur.as_mut().unwrap().next = l2;
        }
        
        D.unwrap().next
    }
}
```

上面的想法概念上講白了就是 3 個 pointer，一個是產生我們的最終答案的串列的 pointer `cur`，一個是 `l1` 的 pointer，一個是 `l2` 的 pointer，藉由不斷移動這些 pointers 來完成

Rust 有嚴格的 ownership 機制，所以當遇到 pointer 問題的時候就會有點煩，因為 Rust 不像其他語言可以那麼自由的使用 pointer reference，每當你創造一個 reference 你就要處理他所指的資料的 ownership 問題
上面的 code 中存在 `as_ref()`, `as_mut()`, `take()`, `unwrap()`, `Some()` 都有 ownership 處理的考量在裡面

所以我們能不能不用 pointer 的想法來處理這題

今天我們要 merge `l1` 和 `l2` 兩串粽子成為一串 `m`
1. 挑 `l1` 和 `l2` 中值比較小的那一個，假設為 `l1`，把他抓出來變成 `m` 的頭，把他的尾巴截掉成為 `l1'`
2. 此時我們就進了一步，知道最終的解 `m` 就是剛剛已經取得的那個值，串上 `l1'` 和 `l2` 合併後的串列
3. 再從 `l1'` 和 `l2` 中選較小得那一個，把他抓出來，把他的尾巴截掉，再去更另一個串列合併，如此重複

``` rust
impl Solution {
    pub fn merge_two_lists(mut l1: Option<Box<ListNode>>, mut l2: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        match (l1, l2) {
            (Some(n1), Some(n2)) => { // take ownership
                if n1.val <= n2.val {
                    Some(Box::new(ListNode {
                        next: Solution::merge_two_lists(n1.next, Some(n2)),
                        val: n1.val
                    }))
                } else {
                    Some(Box::new(ListNode {
                        next: Solution::merge_two_lists(Some(n1), n2.next),
                        val: n2.val
                    }))
                }
            },
            (None, Some(n)) | (Some(n), None) => Some(n),
            (None, None) => None
        }
    }
}
```

Recursion 在純 functional programming 以外的語言其實都不太被鼓勵，因為有 stack 不段重複堆疊的問題。很多語言也都對這件事情有多多少少優化
大部分有效的優化都是 **TCE** (Tail Call Elimination)，他基於 **Tail Call**

> function is tail recursive if the recursive call is the last thing executed by the function. 

這種狀況 compiler 才能做 TCE

Rust 的編譯器採用 LLVM (Low Level Virtual Machine)，Rust 編譯會先把原始碼編譯成 IR，接著才是 從 IR 產生 Assembly 組合語言

> Apple 的 Swift 语言使用 LLVM 作为编译器框架
Rust 使用 LLVM 作为工具链的核心组件
例如，Clang 这个 C/C++ 编译器本身就是一个以 LLVM 为准绳的项目
Kotlin，它名义上是一种 JVM 语言，使用称为 Kotlin Native 的语言开发，该语言也使用了 LLVM 编译机器原生代码。
[LLVM：Swift、Rust、Clang 等语言的强大后盾- Serdar Yegulalp, 盖磊](https://www.infoq.cn/article/what-is-llvm-the-power-behind-swift-rust-clang-and-more)

而 LLVM 對於非 tail recursive 的狀況也有做優化，叫做 **TCO** ([Tail call optimization](https://llvm.org/docs/CodeGenerator.html#tail-call-optimization))，但這並不是保證的一件事情，還是有可能有 stack overhead
所以比較好的狀況還是想辦法把你的 recursion 搞成 tail recursive

## Source Code (C)

``` c
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     struct ListNode *next;
 * };
 */

struct ListNode* mergeTwoLists(struct ListNode* l1, struct ListNode* l2){
    struct ListNode D = { 0, NULL };

    struct ListNode *c = &D;
    while (l1 != NULL && l2 != NULL) {
        if (l1->val <= l2->val) {
            c->next = l1;
            l1 = l1->next;
        } else {
            c->next = l2;
            l2 = l2->next;
        }
        c = c->next;
    }

    if (l1 != NULL) {
        c->next = l1;
    }
    if (l2 != NULL) {
        c->next = l2;
    }

    return D.next;
}
```

## Source Code (C++)

``` cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        ListNode dummy = ListNode();
        ListNode *cur = &dummy;

        while (l1 && l2) {
            if (l1->val <= l2->val) {
                cur->next = l1;
                l1 = l1->next;
            } else {
                cur->next = l2;
                l2 = l2->next;
            }
            cur = cur->next;
        }
        
        if (l1) cur->next = l1;
        if (l2) cur->next = l2;
        
        return dummy.next;
    }
};
```
