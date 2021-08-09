---
title: 'Compile. gcc 編譯 static link, dynamic link, dynamic load'
categories: software
tags:
  - compile
keywords:
  - static link
  - dynamic link
  - dynamic load
  - compile
  - compiler
  - 編譯
date: 2021-06-20 23:20:37
img: https://drive.google.com/uc?export=view&id=1bceNhzHYef4DkW1iNGxt9TMuBRpu2eOS
summary: static link, dynamic link, dynamic load
---


> Reference
PTT LinuxDev, 作者: cole945 (躂躂..) 看板: LinuxDev, 標題: [心得] 用gcc 自製Library, 時間: Sun Nov 5 04:15:45 2006

---

## Static Link

**Compile 時將 library 加入程式碼，執行快但佔空間，code size 和 memory 使用都比較多**

1. Compile source codes to generate object files (-c 編出 object 檔)

``` bash
$ gcc -c file1.c file2.c file3.c
```

2. Create a static library named `libmylib.a`，把一堆 object 檔用 `ar`(archiver) 包裝集合起來，檔名以 `.a` 結尾

``` bash
$ ar rcs libmylib.a file1.o file2.o file3.o
```

3. Using a Static Library to generate a executable files

    `-L`: the directory of the library. 可以指定多次 -LDIR)

    `-l`: the name of the library (注意 *mylib* 這個名稱的使用都不變)

``` bash
$ gcc -o main main.c -L. -lmylib
```

---

## Dynamic Link

**Compile 階段時不將 library 加入程式碼，執行程式的時後再將 library 載入程式碼，若有多個程式共用同一個 library，只需載一個 library 進 memory**

1. Compile source code

``` bash
$ gcc -c -fPIC file1.c file2.c file3.c
```

`-fPIC` 表示要編成 position-independent code，這樣不同 process 載入 shared library 時，library 的程式和資料才能放到記憶體不同位置。較通用於不同平台，但產生的 code 較大，而且編譯速度較慢

2. Create a shared library named libmylib.so

``` bash
$ gcc -o libmylib.so.1.0.0 -shared -Wl,-soname,libmylib.so.1 file1.o file2.o file3.o
```

* so name：用來表示是一個特定 library 的名稱，像是 `libmylib.so.1` 。<br/>以 lib 開頭，接著是該 library 的名稱，然後是 `.so`，接著是版號，用來表名他的介面；如果介面改變時，就會增加版號來維護相容度。<br/>在 mac 上我遇到 `ld: unknown option: -soname`，須以 `-install_name` 取代 `-soname`。<br/>可以用 objdump 檢查 `$ objdump -p libmylib.so | grep SONAME`
* real name：是實際放有 library 程式的檔案名稱，即是 soname 後面再加上 minor 版號與 release 版號，像是 `libmylib.so.1.0.0`。一般來說版號的改變規則是，最尾碼的 release 版號用於程式內容的修正，介面完全沒有改變。中間的 minor 用於有新增加介面，但相舊介面沒改變，所以與舊版本相容。最前面的 version 版號用於原介面有移除或改變，與舊版不相容時
* linker name：是用於連結時的名稱，是不含版號的 so name ，如 `libmylib.so`。通常 linker name 與 real name 是用 ln 指到對應的 real name ，用來提供彈性與維護性

3. Create link

``` bash
$ ln -s libmylib.so.1.0.0 libmylib.so
$ ln -s libmylib.so.1.0.0 libmylib.so.1
```

4. Using a Shared Library

``` bash
$ gcc -o main main.c -L. -lmylib
```

* linker 會搜尋 `libmylib.so` 來進行連結，如果目錄下同時有 static 與 shared library 的話，會以 shared 為主。使用 `-static` 參數可以避免使用 shared 連結

* 可以用 `ldd` 看編譯出的執行檔與 shared 程式庫的相依性 (mac 上改用 `otool -L`)

``` bash
$ otool -L a.out
a.out:
 libmylib.so.1 (compatibility version 0.0.0, current version 0.0.0)
 /usr/lib/libSystem.B.dylib (compatibility version 1.0.0, current version 1252.200.5)
```

5. Execute

``` bash
$ ./main
```

`libmylib.so.1` 可以被建在其他 path，但為了能 dynamic load，該 path 即需要加入 `LD_LIBRARY_PATH`

``` bash
$ LD_LIBRARY_PATH=/another_path ./main
```

Mac 上須改變的是 `DYLD_LIBRARY_PATH`

Remember to put `libmylib.so` into `PATH`, e.g. `/usr/lib`

---

## Dynamic Load

**Compile 時不將 library 加入程式碼，執行程式時遇到函式才將 library 載入，用完後再 free 出空間，執行效果最慢。像 Windows 所用的 DLL ，在使用到時才載入，編譯連結時不需要相關的 library。動態載入庫常被用於像 plug-ins 的應用**

1. Compile source code

``` bash
$ gcc -c file1.c file2.c file3.c
```

2. Create a shared library named `libmylib.so`

``` bash
$ gcc -o libmylib.so.1.0.0 -shared -Wl,-soname,libmylib.so.1 file1.o file2.o file3.o
$ ln -s libmylib.so.1.0.0 libmylib.so
$ ln -s libmylib.so.1.0.0 libmylib.so.1
```

Using a Shared Library- Use the `dlopen` to access the shared library

* #include <dlfcn.h>
* void *dlopen(const char *filename, int flag)
* char *dlerror(void)
* void *dlsym(void *handle, const char *symbol) 取得 symbol 指定的 symbol name 在 library 被載入的記憶體位址
* int dlclose(void *handle)

``` c
    #include "dlfcn.h"
    #include <stdio.h>
    #include <stdlib.h>

    int main()
    {
        void *handle;
        void (*f)();
        char *error;

        /* open the needed object */
        handle = dlopen("./libmylib.so", RTLD_LAZY);
        if (!handle) {
            /* get diagnostic information */
            fputs(dlerror(), stderr);
            exit(1);
        }

        /* find the address of function and data objects */
        f = dlsym(handle, "function1");
        if ((error = dlerror())!=NULL) {
            fprintf(stderr, "%s\n", error);
            exit(1);
        }

        /* excute function1 */
        f();

        /* close a dlopen object */
        dlclose(handle);
    }
```

3. Compile. Since above function are implemented in the library `libdl.a`, we need to load this library

``` bash
$ gcc dltest.c -ldl
```

* `-ldl`: load the library libdl.a
* 與 dynamic link 差別就在 compile 的時候不需要去 link so
