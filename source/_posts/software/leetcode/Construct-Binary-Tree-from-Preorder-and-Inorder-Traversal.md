---
title:
  - '[LeetCode] #105 Construct Binary Tree from Preorder and Inorder Traversal'
categories: software/leetcode
hide: true
summary: 提供 Preorder 和 Inorder 順序，重建出整棵樹
date: 2020-08-28 09:51:16
---

## Problem

提供 Preorder 和 Inorder 順序，重建出整棵樹

* [Link](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)
* 等級：**Medium**
* 推薦指數：[:star::star::star::star:] 算是經典題吧，解法沒做過不容易想到，要特別記一下

> :star: 有人推薦過的題目的我才會紀錄，所以即使我覺得只有一顆星他依舊是一題有其他人推薦的題目，只是我自己不覺得需要刷
> :star::star: 代表我覺得有時間再看就好
> :star::star::star: 代表可以刷
> :star::star::star::star: 代表一定刷
> :star::star::star::star::star: 代表多刷幾次甚至把所有 solution 都弄懂

## 想法

inorder traversal 是 `左 -> 中 -> 右`，所以可以觀察到一個特性就是，對於一個 inorder traversal 的數列，若知道 root 是誰，那該點的左側就都是屬於其左子樹，該點的右側就都是屬於其右子樹
比如 `[1, 9, 2, 3, 15, 20, 7]`，如果知道 `3` 是 root，那其左邊的 `[1, 9, 2]` 其實就是 3 這個 root 的左子樹的 inorder traversal；其右邊的 `[15, 20, 7]` 就是右子樹的 inorder traversal
那接下來問題就是，我們怎麼知道一個 inorder traversal 數列中的 root 是誰？這就要藉由 preorder traversal 數列的幫助，我們可以發現 preorder traversal 數列的第一個數就是整棵樹的 root
比如若 preorder traversal 是 `[3, 9, 1, 2, 20, 15, 7]`，我們就知道整顆樹的 root 是 `3`

那這感覺可以是個 recursion，只要我們持續能知道每一段 Inorder traversal 的 root 是誰，就能夠用 recursion 來做，可能會長得像

```
buildTree(inorder_sequence):
    root = get_root(inorder_sequence)
    new_node = make_node(root)
    new_node.left = buildTree(inorder_sequence[:root])
    new_node.right = buildTree(inorder_sequence[root+1:])
    return new_node
```

所以現在關鍵就在 `get_root`，這裏的做法就有些微妙
上面的 recursion，第 4 行是先 build 左子樹，第 5 行再 build 右子樹，所以若把這個 recursion 的整個 call stack 展開來，會發現所有可能的左子樹都會先被 build 出來，才會輪到右子樹
而以這個展開的順序，每次執行到 `get_root` 的時間點，我們會發現 `get_root` 需要回傳的答案，就會依照 preorder traversal 的順序

接續剛剛的例子，`3` 的左子樹的是 inorder traversal 是 `[1, 9, 2]`，`9` 就會是他的 root，因為 preorder traversal 的第二個數就是 `9`
所以 `[1, 9, 2]` 又以 `9` 為 root 被分兩半進入 `buildTree`，左半是 `[1]`，右半是 `[2]`
對於 `[1]`，他的 root 是 `[1]`，這是來自 preorder traversal 的第三個數就是 `1`
到這裡左邊就都走完了，`buildTree` 會來到處理 `[2]` 的部分，他的 root 是 `[2]`，這是來自 preorder traversal 的第四個數就是 `2`
`buildTree` 接著來到處理 `[15, 20, 7]` 的部分，他的 root 是 `[20]`，這是來自 preorder traversal 的第五個數就是 `20`

這大概就是本題關鍵的地方了
其實還是可以大概感覺一下，因為 inorder traversal 是 `左 -> 中 -> 右`，preorder traversal `中 -> 左 -> 右`
我們的 recursion 的寫法會先一直極力往左走，而 preorder traversal 其實也是，只要我們每次都把頭 pop 掉，他也是一直往左走

``` cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    int preorder_index;
    unordered_map<int, int> inorder_index_map;
    vector<int> preorder_seq;

    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        preorder_index = 0;
        preorder_seq = preorder;
        
        for (int i=0; i<inorder.size(); i++) {
            inorder_index_map[inorder[i]] = i;
        }
        
        return buildTreeFromInorder(0, inorder.size()-1);
    }
    
    TreeNode* buildTreeFromInorder(int start, int end) {
        if (start > end) { return nullptr; }
        
        int root_val = preorder_seq[preorder_index++];
        TreeNode *new_node = new TreeNode(root_val);
        new_node->left = buildTreeFromInorder(start, inorder_index_map[root_val]-1);
        new_node->right = buildTreeFromInorder(inorder_index_map[root_val]+1, end);
        
        return new_node;
    }
};
```
