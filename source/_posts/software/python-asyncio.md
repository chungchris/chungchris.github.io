---
title: Python Asyncio
categories: software
tags:
  - python
  - coding
  - software
  - multithread
keywords:
  - python
  - asyncio
  - python asyncio
  - multithread
date: 2021-06-19 07:34:20
img: https://drive.google.com/uc?export=view&id=1F0iUOrLUW3LUjUblbZ32iyuE6sqTpoaT
summary: 本篇整理了 asyncio 在跨 thread 的三種情境，推薦大部分時候適用的最簡單那個做法
---

本篇整理了 asyncio 在跨 thread 的三種情境，推薦大部分時候適用的最簡單那個做法

## 前言

本篇對於 event loop 的假設皆為最典型的運用，即 main thread 上跑一個 asyncio event loop。

基本上並不建議混用 `threading` 和 `asyncio`，但依然會有本篇的需求原因是，即使單一 app/module 沒有混用 `threading` 和 `asyncio`，也會有需要和其他 app/module 互動的狀況。
身為一個採用 asyncio 設計的應用程式，如何安全的使用其他應用提供的接口，以及如何提供安全的接口給其他應用使用，是本篇的撰寫動機。

之所以會有這篇整理是因為，若我們要用 `asyncio` 用來取代 multithread，會有一個關鍵差異是，multithread 仰賴於 CPU 分時，所以當各項任務跑在各個 thread，基本上如果哪個任務佔住了 CPU（就比如 time.sleep()），其實他終究無法真正獨佔住 CPU 那麼長時間，因為還有底層的 CPU 分時和 multicore 在運作，這不是他能控制的。但 asyncio 就不一樣了，他跑在單一 thread，在一個 event loop 上輪流執行多項任務，所以如果某個任務卡住了 event loop，那 loop 中排程的其他任務也就都被卡著無法執行了。所以，從不影響其他任務的角度來說，我們在使用 `asyncio` 時，要儘量避免有 blocking 的狀況，要改以 aysncio style 的 blocking，也就是說該 flow 是 block 住了，但事實上不會佔住 event loop 不釋放，讓其他 task 得以繼續在 event loop 上被執行。

（本篇假設為 Pyhton 3.5 以後，所以不會看到 yield）

<br />

使用 asyncio 會有以下三種情境

## 三種情境

A. 在 event loop 中跑某個 task 時，想要將另一件 task 也排程進 event loop 被執行 (沒有跨 thread)
B. 在 event loop 中跑某個 task 時，想要呼叫跑在其他 thread 的 API，或者說想要交付某個工作在其他 thread 執行，並等待其結果
C. 其他 thread 的工作想要呼叫跑在 event loop 的 API，換句話說，其他 thread 想交付工作到 event loop 裡執行

<br />

---

## A.在 event loop 中跑某個 task 時，想要將另一件 task 也排程進 event loop 被執行 (沒有跨 thread)

假設現在正在跑的程式段叫做主線任務，我們可以觸發一個支線任務，支線任務其實就是一個 function call。主線任務與支線任務的關係有兩種，一種是不需要等待支線任務結束，也就是說只要觸發就行了，主線任務可以直接繼續跑。一種是要等待支線任務結束，可能是為了執行順序上的需要，更多時候是為了取得支線任務的產出或結果。

### await

對於需要等待其結果的，往往運用 `await` 就足夠。`await` 後面可接 awaitable objects: `Coroutines`, `Tasks`, 或 `Futures`。

> Coroutines  
A coroutine function: an async def function

> Tasks  
Tasks are used to schedule coroutines concurrently.  
When a coroutine is wrapped into a Task with functions like asyncio.create_task() the coroutine is automatically scheduled to run soon

> Futures  
A Future is a special low-level awaitable object that represents an eventual result of an asynchronous operation.

本篇旨在建議如何運用，不涉及其差異的討論。只要知道 Python 裡面每個 function 如果前面加上 `async def`（此種 function 就叫做 coroutine function），那他就成為一個 `awaitable` object，可以被接在 `await` 的後面。
`await` 是在 `Python 3.5` 導入，取代之前的 `yield`，讓語法變得更可讀，他背後其實就是用 `yield` 在實現暫停掛起函數的效果 ，「掛起」的意思就是將控制權交給事件循環 (event loop) 並等待返回。
`await` 搭配 `async def` 在實務使用上真實意義，我們在另一篇描述。

### ensure_future / call_soon

對於不需要等待結果的，這裡有兩個選擇 `ensure_future()` 跟 `call_soon()`，設計上雖然有些不同，但說多數時候可以用 `call_soon` 的地方也可以換成 `ensure_future`，反之亦然。

``` python
loop.call_soon(callback, *args, context=None)  
```

> Schedule the `callback` to be called with args arguments at the next iteration of the event loop.  
Callbacks are called in the order in which they are registered. Each callback will be called exactly once.  
An optional keyword-only context argument allows specifying a custom `contextvars.Context` for the callback to run in. The current context is used when no context is provided.  
An instance of  asyncio.Handle   is returned, which can be used later to cancel the callback.  
This method is not thread-safe.

從上面描述可以看到，`call_soon()` 主要是被設計來給 `callback` 使用，意思就是我要把一個 callback function 放進 event loop 排程被執行。

``` python
asyncio.ensure_future(obj, *, loop=None)
```

> Return:
>  
> - obj argument as is, if obj is a `Future`, a `Task`, or a Future-like object (`isfuture()` is used for the test.)
>  
> - a Task object wrapping obj, if obj is a coroutine (`iscoroutine()` is used for the test); in this case the coroutine will be scheduled by `ensure_future()`.
>  
> - a Task object that would await on obj, if obj is an awaitable (inspect.`isawaitable()` is used for the test.)
>  
> If obj is neither of the above a `TypeError` is raised.
> Important: See also the `create_task()` function which is the preferred way for creating new `Tasks`.

從上面描述可以看到，`ensure_future()` 就是要把一個 `task` 放進 event loop，而 `task` 往往就是一個 function object 的形式，所以其實最後長得就跟 `call_soon()` 一樣，看起來都是把一個 function 排程進 event loop。
我自己都是用 `ensure_future()` 因為他可以直接把 arguments 寫在括號裡面，看起來比較直覺。
（這部分也可以看出他們設計本意上的不同，一個是 `callback`，所以有 context 考量，一個是傳遞一個 awaitable object，參數都在產生 object 時包含進去，本篇就不在這細節部分著墨）

另外最後一句可以注意一下， `create_task()` 和 `ensure_future()` 的差別就在，`ensure_future()` 只是把 `task` 放進去等待被執行，需要 event loop 有在跑才行；而 `create_task()` 則是同時就會確保 event loop 有在跑，如果本來沒在跑，就會把他跑起來。實務上因為我們常常在 main thread 跑起來的開頭處就用了 `loop.run_forever()`，所以 loop 其實一直都在跑，這種狀況下只要用 `ensure_future()`，`task` 就都會被執行到。

---

## B. 在 event loop 中跑某個 task 時，想要呼叫跑在其他 thread 的 API，或者說想要交付某個工作在其他 thread 執行，並等待其結果

當我們的程式採用 `async` 設計，但常常也有需要透過外部 API 拿到某個值後 flow 才能繼續走下去的狀況，但又不能 block 住在那邊傻等。因為如果該外部 API 是一個 IPC API，那對方若晚回應甚至沒回應，如果傻等，豈不是我們的 event loop 就卡在這邊等，但 event loop 上明明還有其他 task 可以在這段時間先跳去做。
有兩種做法，取決於外部 API 是怎麼返還我們在等的結果？是透過 return 還是透過 callback。也就是說，我們所關心的「結果值」，是透過 return 返還來的，還是說會透過我們提供過去的 callback function 傳過來。
如果是只關心 return 值的狀況，做法就是掛起當前的 task，然後起一個 thread 在上面去 call 外部 API 並等待 return，該 API return 後，再將結果值設回 event loop，相當於告知可以在接下來的排程繼續剛剛掛起的地方執行了，這種方法運用到的就是 `run_in_executor()`

``` python
awaitable loop.run_in_executor(executor, func, *args)¶
```

> Arrange for func to be called in the specified executor.  
The executor argument should be an `concurrent.futures.Executor` instance. The default executor is used if executor is `None`.

``` python
import asyncio
import time

async def task_a():
    for i in range(10):
        print(f'task_a: {i}')
        await asyncio.sleep(1)

def task_b(i):
    print(f'task_b')
    time.sleep(4)  # simulate calling an external api
    return i + " world"

async def task_c():
    print(f'task_c: put a task to executor')
    result = await event_loop.run_in_executor(None, task_b, "hello")
    print(f'task_c: result: {result}')

event_loop = asyncio.get_event_loop() 
asyncio.ensure_future(task_a(), loop=event_loop)
asyncio.ensure_future(task_c(), loop=event_loop)
event_loop.run_forever()
```

這段 code 我們假設 `task_b` 裡面包了一個 call external API，模擬該 API 花 4 秒時間才會返還。而我們的 main thread 上，有著 `task_a` 這項一直需要進行的工作，另外還有一個 `task_c`，會需要用到包在 `task_b()` 裡的 external API。我們預期 `task_c` 在等待 `task_b` 裡的 external API 有結果的同時，這個等待期間，main loop 應該還是能要繼續處理 `task_a` 的工作。所以執行結果就是

``` bash
task_a: 0
task_c: put a task to executor
task_b
task_a: 1
task_a: 2
task_c: result: hello world
task_a: 3
task_a: 4
task_a: 5
...
```

上面是假設外部 API 是以 `return` 的方式來返還結果，如果是外部 API 會以 `callback` 的方式來返還結果，也就是說，他可能很快就 return 了，但 return 值可以能只是表示他有收到 request 了，而實際上我們需要的結果，可能在稍後才會透過 callback 傳給我們。
我們會需要掛起原本的 flow，然後在 callback function 裡面實作告知可以在剛剛掛起的地方繼續執行的邏輯，這種就需要運用到操作 `future` 物件，因為 `future` 就是一個可以讓 asyncio 知道什麼時候該掛起什麼時候可以繼續執行的物件。

``` python
loop.create_future()
```

> Create an asyncio.Future object attached to the event loop.  
This is the preferred way to create Futures in asyncio. This lets third-party event loops provide alternative implementations of the Future object (with better performance or instrumentation).

``` python
set_result(result)
```

> Mark the `Future` as done and set its result.  
Raises a `InvalidStateError` error if the `Future` is already done.

看看這個例子

``` python
import asyncio
import queue
import time
import threading

def my_thread():
    print('my_thread: started')
    while True:
        job = q.get()
        value = job.get('value')
        callback = job.get('callback')
        print(f'my_thread: got job, with value {value}')
        time.sleep(4)
        callback(value*value)

async def task_a():
    for i in range(10):
        print(f'task_a: {i}')
        await asyncio.sleep(1)

async def task_b(i):
    def callback(result):
        print(f'callback got result:{result}')
        nonlocal fut
        fut.set_result(result)

    print(f'task_b')
    fut = event_loop.create_future()
    q.put({'value': i, 'callback': callback})
    result = await fut
    print(f'task_b got result:{result}')

    return result

q = queue.Queue()
threading.Thread(target=my_thread).start()

event_loop = asyncio.get_event_loop() 
asyncio.ensure_future(task_a(), loop=event_loop)
asyncio.ensure_future(task_b(5), loop=event_loop)
event_loop.run_forever()
```

這裏起了個 thread 假裝是外部 module，我們會需要它提供的服務，而他的服務很簡單，就是把我們傳進去的值平方，但他要花 4 秒的時間。
第 29 行，我們很輕易的就能把 request 傳給外部 module，他稍後會透過 callback 把他的服務產出傳回來。
所以就會看到我們用 `create_future` 創造一個 `future` 物件，在 30 行 `await` 掛起，意思就是要 asyncio 等待此 future 完成的時候才繼續往下走，而讓此 future 轉變為完成狀態的方式就是透過 `set_result()`。所以我們在 callback function 的實作裡面，就把外部 API 傳來的結果傳到 `set_result`，這樣同時達到告知此 future 已完成的效果，且也有傳遞結果的功用。所以執行起來就是

``` bash
my_thread: started
task_a: 0
task_b
my_thread: got job, with value 5
task_a: 1
task_a: 2
task_a: 3
callback got result:25
task_b got result:25
task_a: 4
task_a: 5
...
```

關鍵就是注意到我們等待 external API 將結果透過 callback 回傳的那段時間，`task_a` 的工作還是能持續被 event loop 處理。

---

## C. 其他 thread 的工作想要呼叫跑在 event loop 的 API，換句話說，其他 thread 想交付工作到 event loop 裡執行

這裏會提兩個 API- `run_coroutine_threadsafe()` 和 `call_soon_threadsafe()`，`run_coroutine_threadsafe` 就是 `call_soon_threadsafe` 的升級版，所以基本上用 `run_coroutine_threadsafe` 即可。

### call_soon_threadsafe()

``` python
loop.call_soon_threadsafe(callback, *args, context=None)
```

> A thread-safe variant of `call_soon()`. Must be used to schedule callbacks from another thread.

用於兩個 thread 間的 context switch，比如 main thread 上跑了 event loop，如果從另外一個 thread call 了一個 API，該 API 的工作內容想要跑在 main thread event loop，那就可以使用此 `call_soon_threadsafe()`，在 API 的一開頭就將 thread 切到 main thread event loop。

關於參數有一個注意事項是

> Most asyncio scheduling functions don’t allow passing keyword arguments. To do that, use `functools.partial()`:

``` python
# will schedule "print("Hello", flush=True)"
loop.call_soon(
    functools.partial(print, "Hello", flush=True))
```

> Using partial objects is usually more convenient than using `lambdas`, as asyncio can render partial objects better in debug and error messages.

也就是說因為沒辦直接像下面這樣用，所以要用 functools.partial 包裝

``` python
loop.call_soon(print, "Hello", flush=True)
```

### run_coroutine_threadsafe()

``` python
asyncio.run_coroutine_threadsafe(coro, loop)
```

> Submit a coroutine to the given event loop. Thread-safe.
> Return a `concurrent.futures.Future` to wait for the result from another OS thread.
This function is meant to be called from a different OS thread than the one where the event loop is running.

用途上跟 `call_soon_threadsafe()` 看起來一樣，都是做 thread 間的工作傳遞，從其他 thread 將工作傳到 main thread event loop 來做。其實 `run_coroutine_threadsafe` 内部就是用了 `call_soon_threadsafe`，而差別就在：
`call_soon_threadsafe`· 參數給一個 `callback`，將該 callback 加入 event loop 的排程，也就是未來會在 main thread event loop 上執行到該 callback 的意思。
而 `run_coroutine_threadsafe` 雖然意思看起來也是把某個 function 丟到 event loop 去做，但他更進一步提供了 return `Future` 物件的功能，透過 `Future.result` 就同時有取得 callback 的回傳值和更重要的是阻塞的效果。

看下面這個簡單的例子，main thread 上跑 event loop，上面本來只跑著 `my_task_a()`，另外有個 `thread1` 將工作 `my_task_b` 交付到 main thread event loop 執行，等於與 `my_task_a` 分享 event loop 來執行，並且執行結果 return 給 `thread1`，值得注意的是第 11 行，`thread1` 透過 `Future.result` 即達成等待的效果。換句話說，如果是想射後不理，不在乎結果，那不用有 `Future.result` 即不會等待。這裡講的等待並不是指阻塞 event loop 運行，而是停住本段 code 繼續往下走，也就是事情還沒做完前不會 return。

``` python
import asyncio
import threading
import time

def my_thread():
    print('my_thread: started')
    time.sleep(2)
    print('my_thread: ask to run my_task_b() on main thread event loop')
    future = asyncio.run_coroutine_threadsafe(my_task_b(5), loop)
    print('my_thread: request sent')
    result = future.result()
    print(f'my_thread result: {result}')

async def my_task_a():
    for i in range(10):
        print(f'my_task_a: {i}')
        await asyncio.sleep(1)

async def my_task_b(value):
    print(f'my_task_b got job')
    countdown = value
    while countdown>0:
        print(f'my_task_b is processing job')
        await asyncio.sleep(1)
        countdown = countdown - 1
    return value*value

threading.Thread(target=my_thread).start()

loop = asyncio.get_event_loop()
asyncio.ensure_future(my_task_a(), loop=loop)
loop.run_forever()
```

執行結果為

``` bash
my_thread: started
my_task_a: 0
my_task_a: 1
my_thread: ask to run my_task_b() on main thread event loop
my_task_a: 2
my_thread: request sent
my_task_b got job
my_task_b is processing job
my_task_a: 3
my_task_b is processing job
my_task_a: 4
my_task_b is processing job
my_task_a: 5
my_task_b is processing job
my_task_a: 6
my_task_b is processing job
my_task_a: 7
my_thread result: 25
my_task_a: 8
my_task_a: 9
```

另外，既然有阻塞，那就需要擔心阻塞多久的問題，我們往往不希望其他 process/thread 的無能造成我們自己本身阻塞太久甚至永遠被阻塞，所以需要設個 timeout 值，這可以透過 result 直接設。

``` python
try:
    result = future.result(timeout)
except asyncio.TimeoutError:
    print('The coroutine took too long, cancelling the task...')
    future.cancel()
```

---

## 結論

使用 asyncio 所會遇到的三種情境，若只有應用程式內部使用

**A. 在 event loop 中跑某個 task 時，想要將另一件 task 也排程進 event loop 被執行 (沒有跨 thread)**

就是用 `await` 和 `ensure_future()`。`await` 用於該 flow 需要等 callee 的結果才能繼續走，`ensure_future()` 用於只是要觸發 callee，不需要等其結果，直接可以繼續走。

<br />

而對於需與其他應用程式交互的情境，分為提供 API 給其他人使用，以及呼叫其他人提供的 API。

**B. 在 event loop 中跑某個 task 時，想要呼叫跑在其他 thread 的 API，或者說想要交付某個工作在其他 thread 執行，並等待其結果**

當呼叫其他人提供的 API 時，我們不希望等待其結果的這段時間，會卡住我們的 event loop 無法運作。這種情境又會分為兩種狀況，取決於我們 call 外部 API 時，我們所需要取得的結果是透過 return 回傳還是透過 callback 回傳。若是透過 return 回傳，就使用 `run_in_executor()`；若是透過 callback 回傳，就需要用到 `create_future()` 創造 `future` 物件，`await` 該物件，然後在 callback 中`set_result()` 告知該 future 已有結果。

<br />

**C. 其他 thread 的工作想要呼叫跑在 event loop 的 API，換句話說，其他 thread 想交付工作到 event loop 裡執行**

當提供 API 給其他人使用時，我們想將其他人交付的工作排程入 event loop，就是用 `run_coroutine_threadsafe()`。

<br />

## 後記

本篇並沒有對各 API 舉詳細的範例及各種用法，不過相信以該 API 為關鍵字 Google 一下就有許多例子了。我想解決的痛點只是 `asyncio` 好像有好幾個名字都很像的 API 不知道什麼時候該使用哪個。
