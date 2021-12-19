---
title: C 語言筆記
tags:
  - c
  - programming language
categories: software
keywords:
  - c
  - coding
  - programming
summary: 一些 C 的個人筆記
date: 2021-08-10 14:45:41
img: 'https://drive.google.com/uc?export=view&id=1aWKS0CtDghddpyvMtpcJSrDRUnula6KV'
top:
hide:
cover:
coverImg:
author:
password:
toc:
mathjax:
reprintPolicy:
---

## Cheat Sheet

* [C Reference Cheat Sheet by Ashlyn Black](https://cheatography.com/ashlyn-black/cheat-sheets/c-reference/)

---

## Pointer

### 2D Array

當 type 被定義為一個二維陣列（array），array + 1 的效果其實是跳了**一維**，也就是到了第二個 row
`array[2][3]`， 想去找到第二個 row 的第三個元素，可用 `*(*(array + 1) + 2)`
* 其中 `*(array + 1)` 先做了一次 deref（取值），其值即為標示第二個 row 的一個 address
* 再對其 +2 此時就是跳 2 個元素，得到第二個 row 的第三個元素的位址，再對其取值（deref）

> 要注意 `*(array + 5)` 不見得會是 `array[2][3]`，應該交給 compiler 去做層層 deref 比較恰當，讓 compiler 去適當轉譯每一個 `+` 的意思

---

## Const, Static

`const` 是用來修飾某 variable identifier 對於 underlying data 的 mutability
`static` 是定義記憶體的 lifetime，如果一個變數被定義為 static，他在被創造之後，直到 program exit 才會消失

### const

const 放置的位置可能取決於 compiler，有的會直接將其展開成 text，所以自然變成融入 text 裡，沒有放置的問題。如果沒有展開的 compiler，那就是放在 data（完整為 initialized data segment），跟 global, static 放置的位置一樣

#### something trivial

也可用來修飾一個指標，修飾指標的意義在於規範 不可透過該指標去 deref 改到指向的 data

* `const int * p`
    * (const) (int (*))
    * 不能透過 `p` 去改該指標所指向的值，也就是不能做 `*p = b`
    * 但可以做 `p = (int*)p2`，也就是 p 本身所代表的位址是可以被改變的
    * 最常用在參數傳遞

``` c
fun1(const int *p_i) {
  // 不預期不希望此 function 會去改到 p_i 指向的值，只希望此 function 把 p_i 當成 const 來用。這裡是用 int 舉例，但最常見的是 const char*
}

int main() {
  int i;
  fun_a(&i);
}
```

* `int * const p`
    * (int (* const))
    * 不能改此變數的值，也就是不能做 `p = (int*)p2`
    * `*p = b` 賦值是可以的

* `int const * p`
    * ((int const) (*))
    * 跟 `const int * p` 是一樣的
    * `p = (int*)p2` 可以
    * `*p = b` 不行

* `const int * const p`
    * ((const) (int (* const)))
    * `p = (int*)p2`、`*p = b`都不行

> 其實如果把 data type 和 * 想在一起，也就是如 `int* const p`，那問題就只剩兩種 `int* const p` 和 `const int* p`，那就可以區分出前者修飾到的是變數，後者修飾到的是指標
> 而 `int const * p` 其實是把後者的 const int 順序調換，效果不變，也就是 `(int const)*` 等於 `(const int)*`
>
> 只是幫這記憶，實際上 `int *` 不會寫在一起的，因為比如
> `int* var1, var2;` 這裏 var1 是指標，var2 不是喔，所以如果把 int* 寫在一起容易誤解

### static

如果一個變數被定義為 static，如果他是一個 function 內的 local variable，即使每次進出這個 function 時該 function 的 stack 都被 allocate/deallocate，但不會影響到 static variable，也就是說第二次進到該 function 時，還會存在上次該 function 對於該 static variable 的記憶
其中原因就是 static variable 並不在 stack，而是在 data（完整為 initialized data segment），跟 global 放置的位置一樣，

``` c
#include <stdio.h>

void count(void);

int main(void) {
    for(int i = 0; i < 10; i++) {
        count();
    }
    return 0;
}

void count(void) {
    static int c = 1;
    printf("%d ", c);
    c++;
}
```

執行結果為

``` bash
1 2 3 4 5 6 7 8 9 10
```

---

## Global, Extern

`global` 處理的是 namespace，也就是作用域（scope）

一個 global 變數的作用域為**全部文檔**，只要其他文檔使用 extern 宣告外部連結性，那就可以看到

但既然扯到了跨文檔，就要進一步提到 compile 和 link
實務上在我們文檔比如 `file1.c` 中會去用到其他文檔比如 `file2.c` 的 function，我們常常透過在 `file1.c ` 中 `include file2.h` 完成這件事
我們常常採取 [Dynamic link](https://chungchris.github.io/2021/06/20/software/compile/#toc-heading-2) 的方式，compile 階段時不將 library 加入程式碼，執行程式的時後 link 階段再將 library 載入程式碼
也就是說，在 compile 階段，只要 `file2.h` 中的確有我們所使用到的符合 signature 的 function，那 compile 階段就可以成功

上述是對於 function 了連結，而同樣的道理也可以適用在變數身上
我們可以透過 `extern` 在 `file1.c ` 去**宣告**一個變數的外部連結性，而不用去**定義**他，效果讓 compile 階段還是可以成功通過
在執行程式的 link 階段，就會去定義的地方使用到該變數

### static global

這個 static 不能直接用 static variable 的 static 來直接理解
`static global` 應該一起看，他就是一種特別的 global，指的是該 global 變數 *不能跨文檔*，也就是其他文檔無法透過 extern 連結到此 global 變數

也就是說，如果真的有需要使用 global variable，那他應該儘量是 `static global`，以免擾亂整個 program 的 namespace 作用域

### const global

這本來沒什麼好特別提的，因為 `const` 形容的是 mutability，所以跟 `global` 形容的是不同領域的東西，分開來看即可
**但是**，`const global` 附帶了一個效果是會把該 variable **限制在本文檔**

如果要創造一個為 const 的，又可跨文檔的 global，那要特別著名為 `extern const global`
注意這裏的 extern 是在擁有該 variable 的文檔就使用的，也就是說擁有者要加上 `extern` 來定義，其他文檔的使用者也要透過 `extern` 宣告外部連結性，擁有者和用的人都要寫到 `extern`（不像一般 global，只有用的人需要寫到 `extern`）

---

## Hash Table

C 並沒有內建的 hash table library，可以使用第三方的 [uthash](https://troydhanson.github.io/uthash/userguide.html)
他其實只是 a single header file `uthash.h`，所以等於是差不多把他的實作好的 hash table 複製貼上過來的意思啦

``` c
#include <stdio.h>   /* gets */
#include <stdlib.h>  /* atoi, malloc */
#include <string.h>  /* strcpy */
#include "uthash.h"

struct my_struct {
    int id;                    /* key */
    char name[10];
    UT_hash_handle hh;         /* makes this structure hashable */
};

struct my_struct *users = NULL;

void add_user(int user_id, char *name) {
    struct my_struct *s;

    HASH_FIND_INT(users, &user_id, s);  /* id already in the hash? */
    if (s == NULL) {
      s = (struct my_struct *)malloc(sizeof *s);
      s->id = user_id;
      HASH_ADD_INT(users, id, s);  /* id: name of key field */
    }
    strcpy(s->name, name);
}

struct my_struct *find_user(int user_id) {
    struct my_struct *s;

    HASH_FIND_INT(users, &user_id, s);  /* s: output pointer */
    return s;
}

void delete_user(struct my_struct *user) {
    HASH_DEL(users, user);  /* user: pointer to deletee */
    free(user);
}

void delete_all() {
  struct my_struct *current_user, *tmp;

  HASH_ITER(hh, users, current_user, tmp) {
    HASH_DEL(users, current_user);  /* delete it (users advances to next) */
    free(current_user);             /* free it */
  }
}

void print_users() {
    struct my_struct *s;

    for (s = users; s != NULL; s = (struct my_struct*)(s->hh.next)) {
        printf("user id %d: name %s\n", s->id, s->name);
    }
}

int name_sort(struct my_struct *a, struct my_struct *b) {
    return strcmp(a->name, b->name);
}

int id_sort(struct my_struct *a, struct my_struct *b) {
    return (a->id - b->id);
}

void sort_by_name() {
    HASH_SORT(users, name_sort);
}

void sort_by_id() {
    HASH_SORT(users, id_sort);
}

int main(int argc, char *argv[]) {
    char in[10];
    int id = 1, running = 1;
    struct my_struct *s;
    unsigned num_users;

    while (running) {
        printf(" 1. add user\n");
        printf(" 2. add/rename user by id\n");
        printf(" 3. find user\n");
        printf(" 4. delete user\n");
        printf(" 5. delete all users\n");
        printf(" 6. sort items by name\n");
        printf(" 7. sort items by id\n");
        printf(" 8. print users\n");
        printf(" 9. count users\n");
        printf("10. quit\n");
        gets(in);
        switch(atoi(in)) {
            case 1:
                printf("name?\n");
                add_user(id++, gets(in));
                break;
            case 2:
                printf("id?\n");
                gets(in); id = atoi(in);
                printf("name?\n");
                add_user(id, gets(in));
                break;
            case 3:
                printf("id?\n");
                s = find_user(atoi(gets(in)));
                printf("user: %s\n", s ? s->name : "unknown");
                break;
            case 4:
                printf("id?\n");
                s = find_user(atoi(gets(in)));
                if (s) delete_user(s);
                else printf("id unknown\n");
                break;
            case 5:
                delete_all();
                break;
            case 6:
                sort_by_name();
                break;
            case 7:
                sort_by_id();
                break;
            case 8:
                print_users();
                break;
            case 9:
                num_users = HASH_COUNT(users);
                printf("there are %u users\n", num_users);
                break;
            case 10:
                running = 0;
                break;
        }
    }

    delete_all();  /* free any structures */
    return 0;
}
```

---

## Variadic functions

``` c
#include<stdarg.h> // macros `va_list`, `va_start`, `va_arg`, `va_end`
#include<stdio.h>

int sum(int num_args, ...)
{
   va_list ap;

   va_start(ap, num_args);

   int val = 0;
   for (int i = 0; i < num_args; i++) {
      val += va_arg(ap, int);
   }

   va_end(ap);
 
   return val;
}

int main(void)
{
   printf("Sum of 10, 20 and 30 = %d",  sum(3, 10, 20, 30) );
   printf("Sum of 4, 20, 25 and 30 = %d",  sum(4, 4, 20, 25, 30) );

   return 0;
}
```
