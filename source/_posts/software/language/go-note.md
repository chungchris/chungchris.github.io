---
title: Go 筆記
tags:
  - go
  - programming language
categories: software
keywords:
  - go
  - golang
  - coding
  - programming
img: 'https://drive.google.com/uc?export=view&id=1cviZ_5SNqD4q0VzzCoJ9eSRqicirTDsU'
summary: 一些 Go 的學習資源整理和個人筆記
date: 2021-07-06 11:58:22
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


以下是推薦的入門步驟

1. [A Tour of Go](https://tour.golang.org/welcome/1)：covers basic syntax and data structures; methods and interfaces; concurrency primitives. 敘述的有些簡略，所以基本上只是給個感覺的用途，搭配 playground 可以直接實驗。只需要 1 ~ 2 天即可走完
2. [Writing Web Applications](https://golang.org/doc/articles/wiki/)：Go 算是一個蠻容易學習的語言，所以可直接開始寫邊寫邊學，官方提供的第一個 project 範例就是一個 Web Application。所以 1 和 2 的角色其實差不多，但也互補。1 個小時就可以走完，可以藉此也確認一下自己的環境有沒有安裝正確
3. [Effective Go](https://golang.org/doc/effective_go)：算是語法基礎入門，很多在剛剛 [A Tour of Go](https://tour.golang.org/welcome/1) 沒有講清楚的部分在這裏有更清楚的敘述，同時也包含了 coding style 的部分，大概 1~2 天可以走完
4. [Advanced Go Concurrency Patterns](https://blog.golang.org/io2013-talk-concurrency)：Go 並沒有一個完整的官方教科書，有的就是 Spec、API doc 等技術文件，至於教學的部分是比較離散的。如本篇就是在介紹 Go 很重要的一個特色 goroutine，影片長度半小時，但因為他其實講蠻快的，所以實際上應該是需要 1~2 個小時

以下是一些撰寫程式時的參考資源

* [Golang cheatsheet](https://quickref.me/golang)
* [The Go Programming Language Specification](https://golang.org/ref/spec)：型別及語法定義
* [std](https://golang.org/pkg/)：所有可以直接 import 的 std 提供的 library，都有提供範例，且網頁上這可以直接執行範例 code，[例如](https://golang.org/pkg/strings/#example_Map)
* [Go by Example](https://gobyexample.com/)：許多典型 pattern 的範例，copy 去改
* [godoc](https://pkg.go.dev/)：收集第三方的 package，可以 import 當 lib 來用

(Official docs are collected [here](https://golang.org/doc/))

---

## Chest sheet

[Chest sheet](https://quickref.me/golang)

### Reference

* [Go by Example](https://gobyexample.com/)
* [godoc](https://pkg.go.dev/)

### Data Type

#### Basic

> `int8`	8-bit signed integer
> `int16`	16-bit signed integer
> `int32`	32-bit signed integer
> `int64`	64-bit signed integer
> `uint8`	8-bit unsigned integer
> `uint16` 16-bit unsigned integer
> `uint32` 32-bit unsigned integer
> `uint64` 64-bit unsigned integer
> `int`	    Both in and uint contain same size, either 32 or 64 bit.
> `uint`	Both in and uint contain same size, either 32 or 64 bit.
> `rune`	It is a synonym of int32 and also represent Unicode code points.
> `byte`	It is a synonym of int8.
> `uintptr` It is an unsigned integer type. Its width is not defined, but its can hold all the bits of a pointer value.

#### Advanced

* [slice](https://chungchris.github.io/2021/07/06/software/language/go-note/#toc-heading-10)

* [string](https://quickref.me/golang)

* **map**

``` go
pair := map[rune]rune {
        ')': '(',
        ']': '[',
        '}': '{',
    }
```

### Idiomatic

> * 名稱大寫開頭是 exported，小寫是 private
> * 分號 `;` 只有 for 中會看到，或當你想要把多個 statements 放在同一行的時候。不是沒有，是 Go 自己會在 compile 時補上
> * default zero value on variable init
> * 只有 `for` loop，但可以當 `while` 用。`if` 和 `for` 的 condition 都不需要小括號
> * `range` 用來 iterate `array`, `slice`, `string`, or `map`, or reading from a `channel`。`range` 的 return 值是彈性接收的，如果只用一個變數去接就是代表只接回傳值中的第一個，也可用 `_` (blank identifier) 跳掉不接的部分。（如果是要產生 index 來 iterate 就像 C 一樣用 `for`）
> * function 可以有複數個 return 值，但 `return` expression 是一定要寫的；另外 function signature 可以直接指定要 return 的變數如 `(n int, err error)`
> * Object 要 implement 哪個 `interface` 只需要直接 implement 該 interface 規範的方法，不需要用 interface name 綁定
> * `%v` can print any value, even arrays, slices, structs, and maps.

---

## Print

``` go
fmt.Printf("Hello %d\n", 23)
fmt.Fprint(os.Stdout, "Hello ", 23, "\n")
fmt.Println("Hello", 23)
fmt.Println(fmt.Sprint("Hello ", 23))
```

> `%v` can print any value, even arrays, slices, structs, and maps.

If you want to control the default format for a custom type, define a method with the signature `String() string` on the type. i.e. implement the `Stringer` interface.

``` go
func (t *T) String() string {
    return fmt.Sprintf("%d/%g/%q", t.a, t.b, t.c)
}
fmt.Printf("%v\n", t)
```

從這裏也可以發現，其實如 print `"%d/%g/%q"` 的背後實作就是去呼叫 `t.a`, `t.b`, `t.c` 的 `String()` 方法。所以，要避免在自己寫 `String()` 方法的產生進入迴圈，例如

``` go
type MyString string
func (m MyString) String() string {
    return fmt.Sprintf("MyString=%s", m) // Error: will recur forever.
}
```

Fix by converting the argument to the basic string type by `string(m)`.

---

## Array / Slice

### Array

* Arrays are values. Assigning one array to another copies all the elements.
* If you pass an array to a function, it will receive a copy of the array, not a pointer to it.
* The size of an array is part of its type. The types `[10]int` and `[20]int` are distinct.

因為這樣的特性，所以 Slice 才是在 Go 中更常見的用法

### Slice

Slices wrap arrays to give a more general, powerful, and convenient interface to sequences of data. Slices hold references to an underlying array, and if you assign one slice to another, both refer to the same array. If a function takes a slice argument, changes it makes to the elements of the slice will be visible to the caller.

``` go
s := make([]string, 0) // cap is 0
s = append(s, "d") // auto reallocate
s[0] = "a"
```

``` go
	a := [...]int{1,2,3,4} // a 是 array
	s := []int{1,2,3,4}    // s 是 slices

	as := a[:]             // as 是 slice
	fmt.Println("as:", as) // as: [1 2 3 4]
	
	as[2] = 9
	fmt.Println("as:", as) // as: [1 2 9 4]
	fmt.Println("a:", a)   // a:  [1 2 9 4]  // 改 as 會改到 underlying 的 a
	
	t := []int{5,6}
	s = append(s, t...) // slice 可以被 append，會自動擴充，append 的參數是列舉而不是 slice，所以要用 ... 展開 slice
	fmt.Println("s:", s) // s: [1 2 3 4 5 6]

	as = append(as, t...) // slice 可以被 append，會自動擴充，即使他原本 underlying 的 array a 是不夠的
	fmt.Println("as", as) // as: [1 2 9 4 5 6]
	
	as[2] = 12
	fmt.Println("as:", as) // as: [1 2 12 4 5 6]
	fmt.Println("a:", a)   // a:  [1 2 9 4]  // as 因爲被擴充過，所以已經不是綁定於 a，所以改 as 不再會改到 a
```

#### Two-dimensional slices 

``` go
type LinesOfText [][]byte     // A slice of byte slices.

text := LinesOfText{
	[]byte("Now is the time"),
	[]byte("for all good gophers"),
	[]byte("to bring some fun to the party."),
}
```

#### append()

``` go
func append(slice []T, elements ...T) []T
```

Append the elements to the end of the slice and return the result. The result needs to be returned because the underlying array may change. This simple example

``` go
x := []int{1,2,3}
x = append(x, 4, 5, 6)
fmt.Println(x)
```

Append a slice to a slice. 

``` go
x := []int{1,2,3}
y := []int{4,5,6}
x = append(x, y...)
fmt.Println(x)
```

compiler 會把 ... 的東西理解為一串參數
比如 caller 要傳一串參數 `a, b, c, d, e` 給某 function，function 就是用 `...` 來接，而他跟 slice 其實有個內部轉換的關係，所以同時也可以透過 `...` 將 slice 還原為一串參數

---

## Type assertions/switches

``` go
package main

import "fmt"

func main() {
	var i interface{} = "hello"

	s := i.(string)
	fmt.Println(s)

	s, ok := i.(string)
	fmt.Println(s, ok)

	f, ok := i.(float64)
	fmt.Println(f, ok)

	f = i.(float64) // panic
	fmt.Println(f)
}
```

``` go
switch v := i.(type) {
case T:
    // here v has type T
case S:
    // here v has type S
default:
    // no match; here v has the same type as i
}
```

If the `switch` has no expression it switches on `true`。Switch 不只可以用在 value，也可以用在 condition

``` go
// Compare returns an integer comparing the two byte slices,
// lexicographically.
// The result will be 0 if a == b, -1 if a < b, and +1 if a > b
func Compare(a, b []byte) int {
    for i := 0; i < len(a) && i < len(b); i++ {
        switch {
        case a[i] > b[i]:
            return 1
        case a[i] < b[i]:
            return -1
        }
    }
    switch {
    case len(a) > len(b):
        return 1
    case len(a) < len(b):
        return -1
    }
    return 0
}
```

There is **no automatic fall through**, but cases can be presented in comma-separated lists.

``` go
func shouldEscape(c byte) bool {
    switch c {
    case ' ', '?', '&', '=', '#', '+', '%':
        return true
    }
    return false
}
```

不過 `break` 還是有用處，就是在想要跳到 switch block 更外圍的地方的時候，透過 label

``` go
Loop:
	for n := 0; n < len(src); n += size {
		switch {
		case src[n] < sizeOne:
			if validateOnly {
				break
			}
			size = 1
			update(src[n])

		case src[n] < sizeTwo:
			if n+1 >= len(src) {
				err = errShortInput
				break Loop
			}
			if validateOnly {
				break
			}
			size = 2
			update(src[n] + src[n+1]<<shift)
		}
	}
```

---

## new() / make()

### new
`new` does not initialize the memory, it only *zeros* it. That is, `new(T)` allocates zeroed storage for a new item of type `T` and returns its address, a value of type `*T`. In Go terminology, it returns a pointer to a newly allocated zero value of type `T`.

Since the memory returned by `new` is zeroed, it's helpful to arrange when designing your data structures that the zero value of each type can be used without further initialization. 

``` go
type SyncedBuffer struct {
    lock    sync.Mutex
    buffer  bytes.Buffer
}

p := new(SyncedBuffer)  // type *SyncedBuffer
var v SyncedBuffer      // type  SyncedBuffer
```

`bytes.Buffer` is "the zero value for Buffer is an empty buffer ready to use." Similarly, `sync.Mutex` does not have an explicit constructor or `Init` method. Instead, the zero value for a `sync.Mutex` is defined to be an unlocked mutex. Both p and v will work correctly without further arrangement.

#### Composite literal

在有需要自己做 init 的狀況，比如不想要 zero value，可以透過大括號來給定 init 值

``` go
func NewFile(fd int, name string) *File {
    if fd < 0 {
        return nil
    }
    return &File{fd, name, nil, 0}
    // return &File{fd: fd, name: name} // with the missing ones left as their respective zero values.
}
```

> 值得一提的是 Go 是採取 garbage collection 的記憶體回收機制，所以 return 一個 local variable 的 address 並在後續使用是沒有問題的

The expressions `new(File)` and `&File{}` are equivalent. 都是給定 zero values

### make

`make(T, args)` serves a purpose different from `new(T)`. It creates **slices, maps, and channels** only, and it returns an initialized (not zeroed) value of type T (not *T). The reason for the distinction is that these three types represent, under the covers, references to data structures that must be initialized before use.

``` go
v := make([]int, 100) // the slice v now refers to a new array of 100 ints
```

---

## defer

`defer` 某個 function 來讓 return 前執行，他的 stack 會 snapshot 當前的狀態而不是 return 前的狀態。The arguments to the deferred function (which include the receiver if the function is a method) are evaluated when the defer executes, not when the call executes. 並且為 LIFO

``` go
for i := 0; i < 5; i++ {
    defer fmt.Printf("%d ", i)
}
```
``` bash
4 3 2 1 0
```

<br/>

``` go
func trace(s string) string {
    fmt.Println("entering:", s)
    return s
}

func un(s string) {
    fmt.Println("leaving:", s)
}

func a() {
    defer un(trace("a"))
    fmt.Println("in a")
}

func b() {
    defer un(trace("b"))
    fmt.Println("in b")
    a()
}

func main() {
    b()
}
```

``` bash
entering: b
in b
entering: a
in a
leaving: a
leaving: b
```

---

## Interface

Interfaces in Go provide a way to specify the behavior of an object: *if something can do this, then it can be used here.*

Interfaces with only one or two methods are common in Go code. By convention, one-method interfaces are named by the method name plus an -er suffix or similar modification to construct an agent noun: Reader, Writer, Formatter, CloseNotifier etc.

### Type assertions
Interface 也可以是 type assertions 的 case

``` go
switch str := value.(type) {
case string: // string 是一個 variable type
    return str
case Stringer: // Stringer 是一個 interface type
    return str.String()
}
```

### Generality

Interface 規範一個物件需要提供的方法的同時，也因此能達成 Generality

``` go
type Block interface {
    BlockSize() int
    Encrypt(dst, src []byte)
    Decrypt(dst, src []byte)
}

type Stream interface {
    XORKeyStream(dst, src []byte)
}

// NewCTR returns a Stream that encrypts/decrypts using the given Block in
// counter mode. The length of iv must be the same as the Block's block size.
func NewCTR(block Block, iv []byte) Stream
```

`Stream` 和 `Block` 都是 interface，所以 NewCTR generally 就是一個能把 `Block` 轉成 `Stream` 的 API，如此便不用每種 Block 搭配上每種 Stream 都要各自有一個方法

我們可以藉由實作某個 Interface 規範的方法而讓某個 struct 成為該 interface，因此任何接收該 interface type 的 function 都可以接收我們的 struct，這個是最常見的用法

### Adapter

另外還有一種用法：Go 的 method 是可以綁定在除了 pointers 和 interfaces 以外的任何東西，綁定在 function 也可以。也就是說當我們將某個 function implement 了某 interface 所規範的 method，他也會成為該 interface
要做到這件事我們要先為該 function 建立一個 type（正如某個 struct 的 method 是綁在該 struct type 上）

``` go
type Handler interface {
	ServeHTTP(ResponseWriter, *Request)
}

type HandlerFunc func(ResponseWriter, *Request)

func (f HandlerFunc) ServeHTTP(w ResponseWriter, req *Request) {
    f(w, req)
}
```

一個有實作 `ServeHTTP` method 的物件就能成為 `Handler` 這種 interface。我們今天自己有一個 singnature 長這樣 `func(ResponseWriter, *Request)` 的 function，我們為他實作了 `ServeHTTP` 這個方法，從此我們的 `HandlerFunc` 的實體也可以用來餵給任何要接收 `Handler` 的地方。

> 其實如果用 struct 來想本來很好理解：比如我們寫了某個 struct `MyHandler`，我們為 `MyHandler` 實作了 `ServeHTTP` 方法，因此他就成為了一個合法的 `Handler`，我們就可以把 `MyHandler` 餵給任何可以接收 `Handler` 的地方。
> 現在只是把 struct 換成 function，這其實也是 functional programming 的應用需求，用 struct object 和用 function object 的差別就是 struct 會有 internal state 而 function 完全是看當下給的參數。

這種用法就像一個 adapter，可以把有 `func(ResponseWriter, *Request)` 這種 signature 的方法，轉成 `Handler` interface。為什麼說「轉」？因為符合 `func(ResponseWriter, *Request)` 這個 signature 的那些方法，本身是沒有個別實作 `ServeHTTP()` 的喔，`ServeHTTP()` 的導入是倚靠我們將他實作在 type `HandlerFunc`

另外也可以注意到，`type HandlerFunc` 是規範 signature，也就是 function name 本身是可以 anonymous 的，只要 signature 長的一樣就會給過，所以就有了我們在 [Writing Web Applications](https://golang.org/doc/articles/wiki/) 看到的用法

``` go
func viewHandler(w http.ResponseWriter, r *http.Request, title string) {
	//...
}

func editHandler(w http.ResponseWriter, r *http.Request, title string) {
	//...
}

func saveHandler(w http.ResponseWriter, r *http.Request, title string) {
	//...
}

func makeHandler(fn func(http.ResponseWriter, *http.Request, string)) http.HandlerFunc { // http.HandlerFunc is suitable to be passed to the function http.HandleFunc
	return func(w http.ResponseWriter, r *http.Request) {
		m := validPath.FindStringSubmatch(r.URL.Path)
		if m == nil {
			http.NotFound(w, r)
			return
		}
		fn(w, r, m[2])
	}
}

func main() {
	http.HandleFunc("/view/", makeHandler(viewHandler))
	http.HandleFunc("/edit/", makeHandler(editHandler))
	http.HandleFunc("/save/", makeHandler(saveHandler))
	log.Fatal(http.ListenAndServe(":8080", nil))
}
```

`viewHandler`, `editHandler`, `saveHandler` 是不符合 `HandlerFunc` signature 的，但我們透過 `makeHandler()` 製造了一個符合 signature 的 closure (anonymous function object)，這樣就可以不用去更改 `viewHandler`, `editHandler`, `saveHandler` 的參數。

### Embedding

``` go
type Reader interface {
    Read(p []byte) (n int, err error)
}

type Writer interface {
    Write(p []byte) (n int, err error)
}

// ReadWriter is the interface that combines the Reader and Writer interfaces.
type ReadWriter interface {
    Reader
    Writer
}
```

Only interfaces can be embedded within interfaces.

---

## Closure

``` go
package main

import "fmt"

// fibonacci is a function that returns
// a function that returns an int.
func fibonacci() func() int { // 這裡的 `func() int` 是 type，代表 fibonacci 這個 function 會 return 一個 `func() int` type 的東西
	a, b := -1, -1

	return func() int { // 所以根據 return type 規範，這裡一定是 `return func() int {}`，這是一個 function object，而不是執行一個 function 的意思，如果是要定義且馬上執行一個 function，要加一個小括號變成 `return func() int {}()`
		switch {
		case a == -1:
			a = 0
			return a
		case b == -1:
			b = 1
			return b
		default:
			a, b = b, a+b
			return b
		}
	}
}

func main() {
	f := fibonacci()
	for i := 0; i < 10; i++ {
		fmt.Println(f())
	}
}
```

More refer to [Codewalk: First-Class Functions in Go](https://golang.org/doc/codewalk/functions/)

---

## Goroutines

* A goroutine is a **lightweight** thread managed by the Go runtime. There might be only one thread in a program with thousands of goroutines. It's not threads, coroutines, or processes.
* Goroutines run in the same address space, so access to shared memory must be synchronized.
* Goroutines are multiplexed onto multiple OS threads so if one should block, such as while waiting for I/O, others continue to run. Their design hides many of the complexities of thread creation and management.
* Go is a concurrent language, not a parallel one.（concurrent 只代表能支援多工，parallel 代表執行時間能減半）

### Channels

*Don't communicate by sharing memory, share memory by communicating.*

如果 gorountines 間 data 都是透過 channel 傳，便可以確保 Only one goroutine has access to the value at any given time.

``` go
ch := make(chan int)

ch <- v    // Send v to channel ch.
v := <-ch  // Receive from ch, and
           // assign value to v.
```

Sends to a buffered channel block only when the buffer is full. Receives block when the buffer is empty.

``` go
ch := make(chan int, 100)
```

#### Close

A sender can close a channel to indicate that no more values will be sent. Only the sender should close a channel, never the receiver. Sending on a closed channel will cause a panic. Closing is only necessary when the receiver must be told there are no more values coming, such as to terminate a range loop.
Receivers can test whether a channel has been closed by assigning a second parameter to the receive expression.

``` go
v, ok := <-ch
```

The loop for i := range c receives values from the channel repeatedly until it is closed. 換句話說，`Close` 很重要的一個用途就是讓 receiver 的 loop 可以終止

### Select

``` go
package main

import "fmt"

func fibonacci(c, quit chan int) {
	x, y := 0, 1
	for {
		select {
		case c <- x:
			x, y = y, x+y
		case <-quit:
			fmt.Println("quit")
			return
        // default:
        // The default case in a select is run if no other case is ready.
        // Use a default case to try a send or receive without blocking:
		}
	}
}

func main() {
	c := make(chan int)
	quit := make(chan int)
	go func() {
		for i := 0; i < 10; i++ {
			fmt.Println(<-c)
		}
		quit <- 0
	}()
	fibonacci(c, quit)
}
```

`for select loop` 在 Go 是一個典型的運用

`select` 的每一個 case 都有 channel，從某 channel 接收或送 item 到某 channel。把 channel 設為 `nil` 可以暫時讓該 case 消失

``` go
    var pending []Item // appended by fetch; consumed by send
    for {
        var first Item
        var updates chan Item // updates 初始是 nil
        if len(pending) > 0 { // 如果 pending 含有意義的值，才把 updates 這個 chan 設起來
            first = pending[0]
            updates = s.updates // enable send case
        }

        select {
        //case // some other case
        case updates <- first: // 當 updates 為 nil，等於沒有這個 case
            pending = pending[1:]
        }
    }
```

下面是另一個例子，`next.After()` 會 return 一個 channel，所以在 `select` 裡面就可以使用它來做到 timer 的效果，但某些狀況我們可能想要暫時不起這個 timer，就可以透過指定成 `nil` 來達成

``` go
const maxPending = 10
...
        var fetchDelay time.Duration
        if now := time.Now(); next.After(now) {
            fetchDelay = next.Sub(now)
        }
        var startFetch <-chan time.Time // startFetch is nil here
        if len(pending) < maxPending {
            startFetch = time.After(fetchDelay) // enable fetch case
        }
```

### Mutex

``` go
package main

import (
	"fmt"
	"sync"
	"time"
)

// SafeCounter is safe to use concurrently.
type SafeCounter struct {
	mu sync.Mutex
	v  map[string]int
}

// Inc increments the counter for the given key.
func (c *SafeCounter) Inc(key string) {
	c.mu.Lock()
	// Lock so only one goroutine at a time can access the map c.v.
	c.v[key]++
	c.mu.Unlock()
}

// Value returns the current value of the counter for the given key.
func (c *SafeCounter) Value(key string) int {
	c.mu.Lock()
	// Lock so only one goroutine at a time can access the map c.v.
	defer c.mu.Unlock()
	return c.v[key]
}

func main() {
	c := SafeCounter{v: make(map[string]int)}
	for i := 0; i < 1000; i++ {
		go c.Inc("somekey")
	}

	time.Sleep(time.Second)
	fmt.Println(c.Value("somekey"))
}
```

Go 有提供 `-race` 來檢查是否存在 race condition 可能性，也就是檢查是否有多個 goroutine access 同一個變數卻沒有 mutex 保護的狀況

``` bash
$ go run -race <*.go>
```

Go 提倡用 communication 取代 data sharing，這本身就會消除很多 data racing。比如很常見的設定一個 `done` flag，讓 thread A 可以去終止跑在 thread B 某個的 loop，這種 pattern 就是透過 share `done` 這塊記憶體來完成，即使這種 pattern 的應用場景本身 data racing 的發生也往往無害，但換成使用 channnel，搭配上 for select 就可以讓他從根本消失

## panic and recover

`panic` creates a run-time error that will stop the program. The function takes a single argument of arbitrary type—often a string—to be printed as the program dies.

**It's also a way to print callstack during debugging.**

When panic is called, including implicitly for run-time errors, it immediately stops execution of the current function and begins unwinding the stack of the goroutine, running any deferred functions along the way. If that unwinding reaches the top of the goroutine's stack, the program dies. However, it is possible to use the built-in function recover to regain control of the goroutine and resume normal execution.

A call to `recover` stops the unwinding and returns the argument passed to panic. Because the only code that runs while unwinding is inside deferred functions, recover is only useful inside deferred functions.
One application of recover is to shut down a failing goroutine inside a server without killing the other executing goroutines.

``` go
func server(workChan <-chan *Work) {
    for work := range workChan {
        go safelyDo(work)
    }
}

func safelyDo(work *Work) {
    defer func() {
        if err := recover(); err != nil {
            log.Println("work failed:", err)
        }
    }()
    do(work)
}
```

這個在抽象化過程是不錯的用法，比如你可能不希望你使用某個外部 lib 所產生的 panic 會導致整個 program 結束
