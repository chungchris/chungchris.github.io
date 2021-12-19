---
title:
  - '[LeetCode] #25 Reverse Nodes in k-Group'
categories: software/leetcode
hide: true
summary: 給一個 linked list，以連續 k 個為一組，將每組都 revert，所最後出現不滿 k 個的組，該組維持不變
date: 2020-08-15 06:11:29
---

## Problem

給一個 linked list，以連續 k 個為一組，將每組都 revert，所最後出現不滿 k 個的組，該組維持不變

* [Link](https://leetcode.com/problems/reverse-nodes-in-k-group/)
* 等級：**Hard**
* 推薦指數：[:star::star::star::star::star:] Revert linked list 升級版

> :star: 有人推薦過的題目的我才會紀錄，所以即使我覺得只有一顆星他依舊是一題有其他人推薦的題目，只是我自己不覺得需要刷
> :star::star: 代表我覺得有時間再看就好
> :star::star::star: 代表可以刷
> :star::star::star::star: 代表一定刷
> :star::star::star::star::star: 代表多刷幾次甚至把所有 solution 都弄懂

---

## Revert linked list

既然他是 revert linked list 的升級版，那我們就先把 revert linked list 做出來
revert linked list 有兩種作法，recursion 或 iteration，我們先用 recursion

``` python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next

def reverse_recursion(node, relink): # return new head
    if node.next:
        ret = reverse_recursion(node.next, node)
    else: # termination
        ret = node
    node.next = relink
    return ret

# usage: reverse_recursion(head, None)
```

## Revert first k nodes of a linked list

上面已經能夠將一個 linked list 給反過來，接著我們進一步把它改成只針對前 k 個動作

``` python
def reverse_recursion(node, relink, k): # return new head
    if k == 1: # termination. revert first 1 node means no revert operation
        ret = node
    else: # k>1
        if node.next:
            ret = reverse_recursion(node.next, node, k-1)
        else:
            ret = None
    if ret:
        node.next = relink
    return ret

# usage: reverse_recursion(head, None, 4) to revert first 4 nodes.
```

注意到，如果不夠 `k` 個，我們藉由返還 `None` 的方式，因為第 16 行的 reassign 是在每個 recursion 的最尾端才做，所以即使是走到最後才發現不夠，`None` 會一路傳回去，所以就有能維持整個 list 沒有任何變動

上面的 `reverse_recursion(head, None, 4)` 會返還一個 new_head，並且為長度 4 的 linked list

但是，如果 linked list 還有剩下，我們其實應該也要想辦法把剩下的返還，因為雖然目前還用不到，但未來肯定會用到
比如 A -> B -> C -> D -> E -> F，當 k 是 3
那上面的 `reverse_recursion` 答案會給出 C -> B -> A
但我們想要 C -> B -> A -> D -> E -> F
因為 D 原本是連在 C 上面，但 Ｃ 後來要變成指向 B，我們也不能因此失去 D 的資訊吧，所以我們應該要將 D 接到 A 上，我們把 D 叫做 next_node
把 code 做點小改變，變成

``` python
def reverse_recursion(node, relink, k): # return (new_head, next_node)                
    if k == 1: # termination
        ret = (node, node.next)
    else: # k>1
        if node.next:
            ret = reverse_recursion(node.next, node, k-1)
        else:
            ret = None
    if ret:
        node.next = relink
        if relink is None:
            node.next = ret[1]
    return ret

# usage: reverse_recursion(head, None, 3) to revert first 3 nodes.
```

第 12 行就把 D 接上去了

## Revert grouped k nodes linked list

接下來我們就要把 input 的 linked list 一批一批呼叫這個 `reverse()` 來把他們反轉
且因為我們的 `reverse()` 會 return new_head 和 next_node，所以我們總是知道要接下來要處理的下一批的頭是誰，也知道該怎麼把已經處理完的上一批的尾，跟已經處理完的下一批的頭連起來
一樣的常見技巧是我們創造一個 dummy node 當作頭，用來連到已經處理完的部分

``` python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def reverseKGroup(self, head: Optional[ListNode], k: int) -> Optional[ListNode]:
        def reverse(node, relink, k): # return (new_head, next_node)                
            if k == 1: # termination
                ret = (node, node.next)
            else: # k>1
                if node.next:
                    ret = reverse(node.next, node, k-1)
                else:
                    ret = None
            if ret:
                node.next = relink
                if relink is None:
                    node.next = ret[1]
            return ret
        
        #return reverse(head, None, 3)[0]

        ''' k=3
        [dummy] [0] [1] [2] [3] [4] [5] [6]
                 |_______|         
          tail
            reverse([0]) -> ([2], [3])
                [2] [1] [0] [3] [4] [5] [6]
            
                tail.next = [2] # dummy.next = [2]
                tail = [0]
        
        [dummy] [2] [1] [0] [3] [4] [5] [6]
                             |_______|
                        tail
            reverse([3]) -> ([5], [6])
                            [5] [4] [3] [6]
            
                tail.next = [5] # [0].next = [5]
                tail = [3]
        
    Temination case A: if the linked list length % k != 0
        [dummy] [2] [1] [0] [5] [4] [3] [6]
                                         |_|
            reverse([6]) -> None
                                    tail
                # do nothing and break loop
        
    Temination case B: if the linked list length % k == 0
        [dummy] [2] [1] [0] [5] [4] [3]
                                    tail
                 # do nothing and break loop
        '''
        dummy = ListNode(val=0, next=head)
        tail = dummy # point to the tail of the finish part. Initialize it as a dummy node.
        while tail.next: # Temination case B
            res = reverse(tail.next, None, k)
            if res is not None:
                tail.next, tail = res[0], tail.next
            else:
                break # Temination case A

        return dummy.next
```

## Revert linked list (Iteration)

一開始有提到 revert linked list 有兩種作法，recursion 或 iteration，我們也試一下 iteration

### Revert whole linked list

一次處理一個 node，用兩個指標 `prev` 和 `cur`

``` python
    '''
        A -> B -> None
   prev cur

        None <- A, B -> None
              prev cur

        None <- A <- B
                prev cur
    '''
    def reverse_iteration(node): # return new head
        cur = node
        prev = None
        while cur:
            temp = cur.next
            cur.next = prev
            prev = cur
            cur = temp
        return prev
    
    #usage: reverse_iteration(head)'''
```

### Revert first k nodes of a linked list

``` python
def reverse_iteration(node, k): # return (new_head, next_node)
    cur = node
    prev = None
    count = 0
    while cur is not None and count < k:
        temp = cur.next
        cur.next = prev
        prev = cur
        cur = temp
        count += 1

    if count != k: # rollback
        reverse_iteration(prev, count)
        return None
                
    node.next = cur

    return (prev, cur)

#usage: reverse_iteration(head, 3)
```

因為是從頭開始走，一路直接就改了 link，所以如果到最後才發現不夠，那就要做 rollback 來恢復原狀

另外一種方法是先直接走到最後，再一路改回來，也是可行，如果剩餘 linked list 長度夠，也就是 n >= k，時間就是 2k，如果不夠，時間就是 n
如果是採用上面這段 code 的 rollback 方法，當剩餘 linked list 長度夠，時間就是 k，如果不夠，時間就是 2n
合理預期在跑一筆測資的時候，剩餘長度夠的狀況應該是比較頻繁的，也就是理論上都是先成功反轉了好幾組，到最後一組才有可能會發生不夠的情形，所以選擇 rollback 的方法應該更好

### Revert grouped k nodes linked list

``` python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def reverseKGroup(self, head: Optional[ListNode], k: int) -> Optional[ListNode]:
        def reverse_iteration(node, k): # return (new_head, next_node)
            cur = node
            prev = None
            count = 0
            while cur is not None and count < k:
                temp = cur.next
                cur.next = prev
                prev = cur
                cur = temp
                count += 1

            if count != k: # rollback
                reverse_iteration(prev, count)
                return None                
                
            return (prev, cur)
        
        #return reverse_iteration(head, 3)[0]
        
        dummy = ListNode(val=0, next=head)
        tail = dummy # point to the tail of the finish part. Initialize it as a dummy node.
        while tail.next: # Temination case B
            res = reverse_iteration(tail.next, k)
            if res is not None:
                tail.next, tail = res[0], tail.next
            else:
                break # Temination case A

        return dummy.next
```

因為我們的 `reverse_iteration` 和之前的 `reverse_recursion` 的功能完全相同，所以其他部分的邏輯完全不需要改變

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
    ListNode* reverseKGroup(ListNode* head, int k) {        
        ListNode *cur = head;
        ListNode dummy = ListNode();
        ListNode *tail = &dummy;
        while (cur) {
            //tail->next = reverseResursive(cur, nullptr, k, nullptr);
            tail->next = reverseIteration(cur, k);
            if (tail->next == nullptr) {
                tail->next = cur;
                break;
            }
            tail = cur;
            cur = cur->next;
        }
        
        return dummy.next;
    }
    
    ListNode* reverseResursive(ListNode* node, ListNode* prev, int k, ListNode** next) {
        if (node == nullptr) return nullptr;
        if (k<1) return node;

        if (prev == nullptr && next == nullptr) next = new ListNode*;
        
        ListNode *ret;
        if (k == 1) {
            ret = node;
            *next = node->next;
        } else if (node == nullptr) {
            ret = nullptr;
        } else {
            ret = reverseResursive(node->next, node, k-1, next);
        }

        if (ret != nullptr) {
            node->next = prev;
            if (prev == nullptr) node->next = *next;
        }
        
        if (prev == nullptr) delete next;

        return ret;
    }
    
    /*
    A -> B -> C -> NULL
        cur = A
            A.next = NULL
        cur = B
            B.next = A
        cur = C
            C.next = B
        cur = NULL
    */
    ListNode* reverseIteration(ListNode* head, int k) {
        if (k<2) return head;

        ListNode *cur = head;
        ListNode *prev = nullptr;
        ListNode *temp;
        int n = 0;
        while (n < k) {
            if (cur == nullptr) {
                reverseIteration(prev, n);
                return nullptr;
            }

            temp = cur->next;
            cur->next = prev;
            n++;

            prev = cur;
            cur = temp;
        }
        head->next = cur;
        return prev;
    }
};
```

其實先走一次，知道了總長之後，就可以比較簡單的處裡尾部的那個 group，並且也不會影響到時間複雜度
