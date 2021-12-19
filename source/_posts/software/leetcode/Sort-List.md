---
title:
  - '[LeetCode] #148 Sort List'
categories: software/leetcode
hide: true
summary: 將一個 linked list 排序
date: 2020-08-19 09:16:12
---

## Problem

將一個 linked list 排序

* [Link](https://leetcode.com/problems/sort-list/)
* 等級：**Medium**
* 推薦指數：[:star::star::star::star::star:] 是 [#21 Merge Two Sorted Lists](https://chungchris.github.io/2020/07/11/software/leetcode/Merge-Two-Sorted-Lists/) 的延伸，題目規範的 `O(n logn)` time and `O(1)` memory (i.e. constant space) 反而成為提示

> :star: 有人推薦過的題目的我才會紀錄，所以即使我覺得只有一顆星他依舊是一題有其他人推薦的題目，只是我自己不覺得需要刷
> :star::star: 代表我覺得有時間再看就好
> :star::star::star: 代表可以刷
> :star::star::star::star: 代表一定刷
> :star::star::star::star::star: 代表多刷幾次甚至把所有 solution 都弄懂

## 想法

如果做過 [#21 Merge Two Sorted Lists](https://chungchris.github.io/2020/07/11/software/leetcode/Merge-Two-Sorted-Lists/) 和 [[#876 Middle of the Linked List]](https://leetcode.com/problems/middle-of-the-linked-list/) 這兩題 Easy 的題目，那這題其實也很容易，不過因為他是兩題 Easy 的延伸，所以作為 Medium 當然沒問題

## Python

``` python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    '''
    These are requests: O(n logn) time and O(1) memory (i.e. constant space)

    Check sorting alogrithm, the ones with O(n logn) time are quick sort, merge sort, heap sort
        - quick sort is not always O(n logn)
        - heap sort needs data put into heap. Our data is in linked list already. We are not allowed to create another heap space
        - merge sort seems to be the one fitting
    
    To sort an array, the space requirement of merge sort is O(n). i.e. need extra same-size space for merging 2 sorted arrays.
    However, here we have is a linked list. We are able to merge 2 sorted linked lists by manipulating node links directly.
    '''
    def sortList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        '''
        merge sort can be bottom-up or top-down
            bottom-up is 2 node in a groups first, merge 2 groups, and so on
            top-down is split the list into 2 groups, split and split until only 1 node in a group, then start merge
        
        we choose top-down here
        '''
        if not head:
            return None
        if head.next is None:
            return head
        mid = self.splitList(head)
        a = self.sortList(head)
        b = self.sortList(mid)
        return self.mergeTwoLists(a, b)
    
    # split the input list into 2 lists from the middle, return the head of the second list
    def splitList(self, head: ListNode) -> ListNode:
        if not head or head.next is None:
            return None

        '''
        [0], [1] => return [1]
        [0], [1], [2] => return [2]
        [0], [1], [2], [3] => return [2]
        [0], [1], [2], [3], [4] => return [3]
        
        In order to cut the list, need to maintain the tail of the first part
        
        [0], [1] => return [1], tail=[0]
        cur
        tail

        [0], [1], [2] => return [2], tail=[1] ***
        cur
             tail

        [0], [1], [2], [3], [4] => return [3], tail=[2] ***
                  cur
                  tail
        
        [0], [1], [2], [3], [4], [5], [6] => return [4], tail=[3] ***
                            cur
                       tail
        
        The cur starts from head.
        loop:
            If the cur can see cur.next.next, move tail for 1 step.
            Move cur for 2 steps.
        '''
        
        # decide the tail of the first part
        cur = head
        tail = head
        while cur.next and cur.next.next:
            tail = tail.next
            cur = cur.next.next
        
        # cut the list
        ret = tail.next
        tail.next = None

        return ret
        
    def mergeTwoLists(self, l1: ListNode, l2: ListNode) -> ListNode:
        D = ListNode(None, None)

        cur = D
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

## C++

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
    ListNode* sortList(ListNode* head) {
        if (head == nullptr) return nullptr;
        if (head->next == nullptr) return head;
        
        ListNode* second_part = split(head);
        head = sortList(head);
        second_part = sortList(second_part);
        return merge(head, second_part);
    }
    
    /* return the head of the last half part */
    ListNode* split(ListNode* head) {
        if (head == nullptr) return nullptr;
        if (head->next == nullptr) return nullptr;
        
        ListNode* cur = head->next;
        ListNode* middle = head;
        bool toggle = false;
        while (cur) {
            cur = cur->next;
            if (toggle) middle = middle->next;
            toggle = !toggle;
        }
        cur = middle->next;
        middle->next = nullptr;
        
        return cur;
    }
    
    ListNode* merge(ListNode* a, ListNode* b) {
        ListNode dummy = ListNode();
        ListNode* cur = &dummy;

        while (a && b) {
            if (a->val <= b->val) {
                cur->next = a;
                a = a->next;
            } else {
                cur->next = b;
                b = b->next;
            }
            cur = cur->next;
        }
        
        if (a) cur->next = a;
        if (b) cur->next = b;
        
        return dummy.next;
    }
};
```
