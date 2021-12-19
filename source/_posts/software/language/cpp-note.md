---
title: C++ 語言筆記
tags:
  - cpp
  - programming language
categories: software
keywords:
  - c
  - c++
  - cpp
  - coding
  - programming
summary: 一些 C++ 的個人筆記
date: 2021-08-10 14:45:45
img: 'https://drive.google.com/uc?export=view&id=1Ie9p-qbOZ9nzja0ijVmEOFNlMTdVijWf'
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

[C++ QUICK REFERENCE / C++ CHEATSHEET](https://github.com/mortennobel/cpp-cheatsheet)

---

## C to C++

* C 不直接支持 pass by reference 因為它 always pass by value，但可藉由傳遞 pointer 自己達成 pass by reference。而 C++ 只要 argument 加上 `&` 就會是指定 pass by reference
* `NULL` 在 C 是 `void*`，在 C++ `__cplusplus` macro 之下會變 `0`；C++ 導入 `nullptr`，取代 C `NULL` 功能，可以指向所有型別
* VLA (variable-length array)
    * array size 可以是變數（事實上是來自於 C99）
    * memory 是在 stack 上，而不像使用 malloc 會是在 heap，也就是說他是 [automatic variable](https://zh.wikipedia.org/wiki/%E8%87%AA%E5%8A%A8%E5%8F%98%E9%87%8F)，在 scope 消滅（如 function return）後生命週期即消失
    * `int a[n];` 出現的位置有講究，必須在 `n` 有意義之後

---

## stringstream

``` cpp
#include <sstream>

stringstream ss("23,4,56");
char ch;
int a, b, c;
ss >> a >> ch >> b >> ch >> c;  // a = 23, b = 4, c = 56
```

---

## static

### class static member

* A static member is shared by all objects of the class. No matter how many objects of the class are created, there is only one copy of the static member.
* All static data is initialized to zero when the first object is created, if no other initialization is present
    * Can't put it in the class definition but it can be initialized outside the class using `<Class>::<static member>=<...>`

### class static function

* A static member function can be called even if no objects of the class exist. The static functions are accessed using only the class name and the scope resolution operator `::`
* A static member function can only access static data member, and other static member functions
* Could use a static member function help to determine whether some objects of the class have been created or not

## virtual, override, final

### override

* override 只用在 function signature 完全相同
* 當有意 override，加上 override 宣告是好習慣，讓 compiler 幫忙檢查 signature 是否相同

``` cpp
class Base {
public:
    virtual void f() {
        std::cout << "Base class default behaviour\n";
    }
};

class Derived : public Base {
public:
    void f() override {
        std::cout << "Derived class overridden behaviour\n";
    }
};
```

* override 的 binding 發生在 compile time，也就是 compile 時就知道是執行哪一個 class 中的實作版本。所以如果有意做 override，就加上 override 關鍵字讓 compiler 幫忙檢查 signature 是否相同
* 當 override 存在，若要指定使用 parent 的版本，要加上如 `child_instance.BaseClass::foo()`

### virtual

[C++ virtual 的兩種用法](https://shengyu7697.github.io/cpp-virtual/)

* pure virtual function 就像其他語言的 interface，用來規範某 class 需要實作某些 method。該定義 pure virtual function 的 class 即為 abstract base class。Abstract base class 只能用來當作 base class，無法被實體化
* virtual function 就是提供 default 實作的 pure virtual function，該 class 可以被實體化
* 基本上只有 base 中定義時要加上 virtual，derived class 中如果要實作自己的版本，virtual 可寫可不寫，但 signature 相同才算有實作到該 virtual
* virtual 的 binding 發生在 runtime，藉由查詢 virtual table 來 binding 到正確的實作版本。virtual table 是屬於 object instance

#### binding

再抓出來看一次差別

* virtual 的 binding 發生在 runtime，藉由查詢 virtual table 來 binding 到正確的實作版本。virtual table 是屬於 object instance
* override 的 binding 發生在 compile time，也就是 compile 時就知道是執行哪一個 class 中的實作版本

``` cpp
#include <iostream>

class A {
public:
    virtual void foo(int) {
        std::cout << "foo(int) of A" << std::endl;
    }
};

class B : public A {
public:
    void foo(int i) { // 這裏實作了 foo 的自己的版本，這是 virtual，但因為 B 不是 base 所以前面可加上 virtual 可不加
        std::cout << "foo(int) of B " << i << std::endl;
    }

    void foo(float i) { // B 中同時存在在 2 個 foo，但 signature 不同，這是多型 polymorphism 的 overloading
        std::cout << "foo(float) of B " << i << std::endl;
    }

    /*void foo(int) const override { // compile error 因為 override 會讓 compiler 檢查與 A::foo 的 signature 是否相同，即使只差一個 const 也是不同，所以會 compile error
        std::cout << "foo(int) const of B" << std::endl;
    }*/
};

class C : public A {
public:
    void foo(float) {
        std::cout << "foo(float) of C" << std::endl;
    }
};

int main ()
{
    int i = 0;
    float f = 1.5;

    A a = A();
    B b = B();

    a.foo(i); // => foo(int) of A
    b.foo(i); // => foo(int) of B 0 // override 在這裏發生作用，這是在 compile time 就已經決定的
    b.foo(f); // => foo(float) of B 1.5 // polymorphism overloading 在這裡發生作用

    A *pA = &a;
    B *pB1 = &b;
    A *pB2 = &b;

    pA->foo(i); // => foo(int) of A
    pB1->foo(i); // => foo(int) of B 0
    pB1->foo(f); // => foo(float) of B 1.5 // polymorphism overloading 在這裡發生作用

    pB2->foo(i); // => foo(int) of B 0
    pB2->foo(f); // => foo(int) of B 1 // 這裡是 virtual 應用的關鍵，為什麼是 1？

    return 0;
}
```

第 57 行是 virtual 應用的關鍵：
`pB2` 是 type `A*`，所以在 runtime 去找 `A` 的 `foo` 要 binding 到哪個版本，也就是 `A` 的 `foo(int)` 要 binding 到哪個版本
而 `pB2` 是實際指向的 instance 是 `b`，`b` 的 virtual table 對於 `foo(int)` 的 binding 是第 12 行的那個。所以這裏其實無關 polymorphism，1.5 變成 1 是 implicit conversion 的結果

> 因為 virtual 是在 runtime 才做 binding，所以會比純 override 效能差一些
> 那為什麼不都用 override 就好，需要有 virtual 存在？因為很多場景我們是無法事先知道 type 的。比如上面的例子直接創了 `A a` 和 `B b` 兩個 instance，但這不符合大部分的 use case，B 繼承 A，很多時候我們可能是根據 runtime 的 input 才知道要創 A 還是 B 比較適合，也就是會用到 `new A()`, `new B()`，同時後續邏輯上又有統一的邏輯對於 A 這個大類別的物件都要 call `foo` 方法的時候，也就是下面這樣

``` cpp
int main ()
{
    int i = 0;
    float f = 1.5;

    A* o[10];
    for (int n=0; n<10; n++) {
       if (n%2 == 0) o[n] = new A();
       else o[n] = new B();
    }

    o[0]->foo(i); // => foo(int) of A
    o[0]->foo(f); // => foo(int) of A
    o[1]->foo(i); // => foo(int) of B 0
    o[1]->foo(f); // => foo(int) of B 1

    return 0;
}
```

> 可以試試看這個 [練習](https://www.hackerrank.com/challenges/hotel-prices/problem) 看看能不能看出問題在哪

### final

* 當一個 function 被加上 final，他的 derived class 就不能 override 此 function
* 當一個 class 被加上 final，他就不能被繼承

---

## Error Handling

``` cpp
try
{
    // protected code
    throw MyException();
}
catch (MyException &e) {
    // catch block
    cout << "exception: " << e.what() << "\n";
}
catch (std::bad_alloc &e) { // exception should be caught by reference
    cout << "exception: " << e.what() << "\n";
    return;
}
catch (std::exception &e) { // exception should be caught by reference
    cout << "exception: " << e.what() << "\n";
}
catch(...) { // handle any exception. a.k.a anonymous exception
    throw;
}

class MyException : public std::exception {
    public:
        virtual const char * what() const throw() {
            return "Attempted to divide by zero!\n";
        }
        // const 代表宣告此 what() 不會造成 object 內容的更改
        // throw() 是個歷史包袱，宣告此 what() 不會在產生任何 exception
};
```

[c++ std exception](https://en.cppreference.com/w/cpp/error/exception)

---

## Overloading Ostream Operator

``` cpp
ostream& operator<<(ostream& os, const Person& p) {
    os << "first_name=" << p.get_first_name() << ",last_name=" << p.get_last_name();
    return os;
}
```

``` cpp
class Matrix {
    public:
        vector<vector<int>> a;
        Matrix operator+ (const Matrix&);
};

Matrix Matrix::operator+ (const Matrix& param) {
    Matrix temp;
    int n = a.size();
    int m = a[0].size();
    for (int i=0; i<n; i++) {
        vector<int> b;
        for(int j=0; j<m; j++) {
            b.push_back(param.a[i][j]+a[i][j]);
        }
        temp.a.push_back(b);
    }
    return temp;
}
```

---

## Templet
