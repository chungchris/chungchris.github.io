---
title:
  - '[LeetCode] #138 Copy List with Random Pointer'
categories: software/leetcode
hide: true
summary: Deepcopy 一個有 random pointer 的 linked list
date: 2020-08-19 06:59:54
---

## Problem

Deepcopy 一個有 random pointer 的 linked list

* [Link](https://leetcode.com/problems/copy-list-with-random-pointer/)
* 等級：**Medium**
* 推薦指數：[:star::star::star::star:] 不使用額外空間的解法沒做過不容易想到

> :star: 有人推薦過的題目的我才會紀錄，所以即使我覺得只有一顆星他依舊是一題有其他人推薦的題目，只是我自己不覺得需要刷
> :star::star: 代表我覺得有時間再看就好
> :star::star::star: 代表可以刷
> :star::star::star::star: 代表一定刷
> :star::star::star::star::star: 代表多刷幾次甚至把所有 solution 都弄懂

## Python

``` python
"""
# Definition for a Node.
class Node:
    def __init__(self, x: int, next: 'Node' = None, random: 'Node' = None):
        self.val = int(x)
        self.next = next
        self.random = random
"""

class Solution:
    def copyRandomList(self, head: 'Node') -> 'Node':
        '''
        * Each node will be linked by `next` only once
        * Each node will be linked by `random` 0-multiple times
        
        We want each node cloned only once:
            Are we going to allow clone node on reading `random`?
                * YES: we are able to correctly assign `random` immediately
                    => need to remember what nodes have already been cloned ... Solution copyRandomListA
                * NO: we are not able to correctly assign `random` immediately
                    => we have to handle assigning `random` later
                       e.g. if A.randon is C, how can we make A'.random = C' later?
                        => need to remember the mapping C->C'
                            * use a map, i.e. dict. e.g. {C: C'}            ... Solution copyRandomListB
                            * utilize original C. make C.next = C'          ... Solution copyRandomListC
        '''
        
        #return self.copyRandomListA(head)
        #return self.copyRandomListB(head)
        return self.copyRandomListC(head)


    # Solution A
    #   Allow clone node on reading `random`
    #   Need to remember what nodes have already been cloned
    def copyRandomListA(self, head: 'Node') -> 'Node': 
        mem = {}
        def _copyRandomList(node: 'Node'):
            if node is None:
                return None
            if node in mem:
                return mem[node]
            mem[node] = Node(node.val)
            mem[node].next = _copyRandomList(node.next)
            mem[node].random = _copyRandomList(node.random)
            return mem[node]

        return _copyRandomList(head)


    # Solution B
    #   Clone node on reading `next`
    #   Not allow clone node on reading `random`
    #   Leave `random` empty. Handle assigning `random` later
    #   Remember the address of clone node by a dict
    def copyRandomListB(self, head: 'Node') -> 'Node': 
        if head is None:
            return None

        cur = head
        mem = {}
        mem[head] = Node(head.val)
        while cur.next:
            mem[cur].next = Node(cur.next.val)
            mem[cur.next] = mem[cur].next
            cur = cur.next

        cur = head
        while cur:
            if cur.random:
                mem[cur].random = mem[cur.random]
            cur = cur.next

        return mem[head]


    # Solution C
    #   Remember the address of clone node by utilizing the `next` field in the original list
    def copyRandomListC(self, head: 'Node') -> 'Node': 
        if head is None:
            return None

        cur = head
        while cur:
            cur.next = Node(cur.val, cur.next)
            cur = cur.next.next
            
        # if original list is A -> B -> C
        # here the list will become A -> A' -> B -> B'

        # assign random
        cur = head
        while cur:
            if cur.random:
                cur.next.random = cur.random.next
            cur = cur.next.next

        # split the list
        #   original list: A -> A' -> B -> B' -> C- > C'
        #   split to
        #       A -> B -> C -> None
        #       A' -> B' -> C' -> None
        cur, cur_clone = head, head.next
        ret = head.next
        while True:
            if cur_clone.next is None:
                cur.next = None
                break
            cur.next, cur_clone.next = cur_clone.next, cur_clone.next.next
            cur, cur_clone = cur.next, cur_clone.next
                
        return ret
```

## C++

``` cpp
/*
// Definition for a Node.
class Node {
public:
    int val;
    Node* next;
    Node* random;
    
    Node(int _val) {
        val = _val;
        next = NULL;
        random = NULL;
    }
};
*/

class Solution {
public:
    Node* copyRandomList(Node* head) {
        Node* cur = head;
        while (cur) {
            Node* clone_node = new Node(cur->val);
            clone_node->next = cur->next;
            cur->next = clone_node;
            cur = cur->next->next;
        }
        
        cur = head;
        while (cur) {
            if (cur->random) cur->next->random = cur->random->next;
            cur = cur->next->next;
        }
        
        cur = head;
        Node dummy = Node(0);
        Node* cur2 = &dummy;
        while (cur) {
            cur2->next = cur->next;
            cur->next = cur->next->next;
            cur = cur->next;
            cur2 = cur2->next;
        }
        
        return dummy.next;
    }
};
```
