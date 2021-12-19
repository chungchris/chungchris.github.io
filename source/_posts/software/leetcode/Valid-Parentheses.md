---
title:
  - '[LeetCode] #20 Valid Parentheses'
categories: software/leetcode
keywords:
  - parentheses
hide: true
summary: Parse 大/中/小 括號對看是否合法
date: 2020-07-10 18:22:19
---

## Problem

Parse 大/中/小 括號對看是否合法

* [Link](https://leetcode.com/problems/valid-parentheses/)
* 等級：**Easy**
* 推薦指數：[:star::star::star::star::star:] 經典題，很容易，但如果沒有做過還是可能會漏一些東西

> :star: 有人推薦過的題目的我才會紀錄，所以即使我覺得只有一顆星他依舊是一題有其他人推薦的題目，只是我自己不覺得需要刷
> :star::star: 代表我覺得有時間再看就好
> :star::star::star: 代表可以刷
> :star::star::star::star: 代表一定刷
> :star::star::star::star::star: 代表多刷幾次甚至把所有 solution 都弄懂

---

## 想法

Parse 括號的特性就是與 stack add/pop 的特性 100% 相符

## Source Code (Python)

``` python
class Solution:
    '''
    As a human,
        ...{...[...(...
            when we see left bound, we just keep them in mind, but without taking any real action. 
        ...{...[...(...)
            only when we see the right bound, we go to find the latest left bound to make a pair, and then we can consume the content in the scope
    
    The key word is "latest". It will be LIFO. A stack can help us to do "keep them in mind".

    Whenever encounter right bound, and if it's not able to pair, it's not valid.
    When the traversal finishs, if there is any left bound remaining in the stack, it's not valid.

    Test cases:
        normal valid: "()[]"
        normal valid: "[()]"
        normal invalid: "([)]"
        normal invalid: "[()"
        special: "("
        special: ")"
    '''
    def isValid(self, s: str) -> bool:
        left = set('([{')
        pair = {')':'(', ']':'[', '}':'{'}

        stack = []
        for c in s:
            if c in left:
                 stack.append(c)
            elif len(stack) == 0:
                return False
            else:
                if pair[c] != stack[-1]:
                    return False
                stack.pop()

        if len(stack) > 0:
            return False
        return True
```

都用 `if ==` 來比較 char 我是覺得也沒什麼問題啦，只是說用 `set` 和 `dict` 因為他們是 hash search，所以理論上會快一些，但對這題來說真差別有限 

## Source Code (Go)

``` go
func isValid(s string) bool {
    pair := map[rune]rune {
        ')': '(',
        ']': '[',
        '}': '{',
    }

    stack := make([]rune, 0)
    for _, c := range s {
        switch c {
        case '(', '[', '{':
            stack = append(stack, c)
        case ')', ']', '}':
            if len(stack) == 0 {
                return false
            }
            c = pair[c]
            if stack[len(stack)-1] != c {
                return false
            }
            stack = stack[:len(stack)-1]
        }
    }
    if len(stack) > 0 {
        return false
    }
    return true
}
```

## Source Code (Rust)

``` rust
impl Solution {
    pub fn is_valid(s: String) -> bool {
        let mut stack = vec![];
        for c in s.chars() {
            match c {
                '(' | '[' | '{' => {
                    stack.push(c);
                },
                _ => {
                    if stack.len() == 0 {
                        return false;
                    }
                    match c {
                        ')' => {
                            if stack.pop().unwrap() != '(' {
                                return false;
                            }
                        },
                        ']' => {
                            if stack.pop().unwrap() != '[' {
                                return false;
                            }
                        },
                        '}' => {
                            if stack.pop().unwrap() != '{' {
                                return false;
                            }
                        },
                        _ => {}
                    }
                }
            }
        }
        if stack.len() > 0 {
            return false;
        }
        return true;
    }
}
```

## Source Code (C)

``` c
bool isValid(char * s){
    int len = 16;
    int *stack = (int*)malloc(sizeof(int)*len);
    int last = 0;
    char *c = s;
    while (*c) {
        switch (*c) {
            case '(':
            case '[':
            case '{':
                last++;
                if (last == len) {
                    len *= 2;
                    stack = realloc(stack, sizeof(int)*len);
                }
                stack[last] = *c;                
                break;
            case ')':
                if (stack[last] != '(')
                    return false;
                else
                    last--;
                break;
            case ']':
                if (stack[last] != '[')
                    return false;
                else
                    last--;
                break;
            case '}':
                if (stack[last] != '{')
                    return false;
                else
                    last--;
                break;
            default:
                break;
        }
        c++;
    }

    if (last != 0) {
        return false;
    }
    return true;
}
```