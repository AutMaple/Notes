# yield 和协程

## yield 的作用是什么？

最初的 python 使用 yield 关键字来声明一个生成器( generator) , 也就是包含有 yield 关键字的函数就是一个生成器

### yield 的语法规则

1. 被 yield 关键字修饰的函数被调用时，不会立即执行，而是返回一个生成器。该函数只有被 next() 函数调用或者生成器它自己调用 send() 方法时，才会执行。
2. 在执行函数时，如果执行到 yield 语句，就暂停函数的执行，并将 yield 关键字后面跟着的表达式返回，如果 yield 关键字后面没有任何表达式，则默认返回 None。执行完 yield 语句之后，该函数进入阻塞状态，要恢复到就绪态需要使用 next() 函数或者 send() 函数
3. 函数被唤醒之后就接着执行 yield 之后的语句，同时函数被唤醒时，yield 语句会有一个返回值，默认是 None。在唤醒函数时，可以通过 send 函数给生成器传值，传递的值会被 yield 语句返回。要注意的一点就是， send() 函数必须传递一个值，不然会报错

```python
def task():
    for i in range(0, 3):
        var = yield
				print("var --> ", var)

if __name__ == '__main__':
    t = task()
    for i in range(0, 3):
        res = next(t)
        print("res: ", res)

# 输出
res:  None
var -->  None
res:  None
var -->  None
res:  None
def task1():
    for i in range(0, 3):
        yield i

if __name__ == '__main__':
    t = task1()
    for i in range(0, 3):
        res = t.send(None)
        print("res: ", res)

# 输出
res:  0
var -->  None
res:  1
var -->  None
res:  2
```

## send() 函数

最初的 yield 函数的作用就是暂停函数并返回，不能够接收外部传递过来的值，后来 python 才为yield 加入这个功能。每个生成器都有一个 send() 方法，生成器可以通过 send() 方法为内部的 yield 传递参数。

此时 yield 语句不再只是 `yield xxxx` 的形式，还可以是 `var = yield xxxx` 的赋值形式。**它同时具备两个功能，一是暂停并返回函数，二是接收外部 send() 方法发送过来的值，重新激活函数，并将这个值赋值给 var 变量！**

## yield from

yield form 是 python 3.3 新出现的语法

### yield form 的作用

1. yield from 后面可以接任何的可迭代对象，并将可迭代对象中的值一个一个的返回
2. 简化代码，使代码逻辑清晰
3. 在复杂的嵌套中，yield from 关键字在调用方和子生成器之间建立了双向通道，可以直接通信

## 协程

**协程，又称微线程，英文名 `Coroutine`**，是运行在单线程中的“并发”，协程相比多线程的一大优势就是省去了多线程之间的切换开销，获得了更高的运行效率

协程的切换不同于线程切换，是由程序自身控制的，没有切换的开销。协程不需要多线程的锁机制，因为都是在同一个线程中运行，所以没有同时访问数据的问题，执行效率比多线程高很多。

### 协程的发展历程

Python 中的协程经历了很长的一段发展历程。最初的生成器 `yield` 和 `send()` 语法，然后在Python 3.4 中加入了`asyncio` 模块，引入 `@asyncio.coroutine` 装饰器和 `yield from` 语法，在Python 3.5 上又提供了 `async/await` 语法，目前正式发布的 Python 3.6 中 `asynico` 也由临时版改为了稳定版。

### 进程，线程和协程的比较

进程/线程：操作系统提供的一种并发处理任务的能力。

协程：程序员通过高超的代码能力，在代码执行流程中人为的实现多任务并发，是单个线程内的任务调度技巧。

### 协程的状态

协程当前状态可以导入 inspect 模块，使用 inspect.getgeneratorstate(...) 方法查看

- `GEN_CREATED` : 等待开始执行。
- `GEN_RUNNING` : 协程正在执行。
- `GEN_SUSPENDED` : 在 yield 表达式处暂停。
- `GEN_CLOSED` : 执行结束。

由于 send 传递的值是 yield 语句的返回值，因此 send 函数的调用只能够在生成器阻塞时才能能够调用

如果协程还没激活(状态是`'GEN_CREATED'`），就立即把 None 之外的值发给它，会出现 TypeError 。因此，始终要先调用 `next(my_coro)` 激活协程（也可以调用 `my_coro.send(None)` ），这一过程被称作预激活。

## **@asyncio.coroutine 与 yield from**

**@asyncio.coroutine：asyncio 模块中的装饰器，用于将一个生成器声明为协程。**

**yield from 其实就是等待另外一个协程的返回。**

```python
def func():
    for i in range(10):
        yield i

print(list(func()))
```

可以写成：

```python
def func():
    yield from range(10)

print(list(func()))
```

从 Python3.4 开始 asyncio 模块加入到了标准库，通过 asyncio 我们可以轻松实现协程来完成异步 IO 操作。asyncio 是一个基于事件循环的异步 IO 模块,通过 `yield from`，我们可以将协程 `asyncio.sleep()`的控制权交给事件循环，然后挂起当前协程；之后，由事件循环决定何时唤醒 `asyncio.sleep`,接着向后执行代码。

下面这段代码，我们创造了一个协程 display_date(num, loop)，然后它使用关键字 yield from 来等待协程 `asyncio.sleep(2)()`的返回结果。而在这等待的2s之间它会让出CPU的执行权，直到 `asyncio.sleep(2)`返回结果。`asyncio.sleep(2)` 模拟的其实就是一个耗时 2 秒的 IO 读写操作。

```python
import asyncio
import datetime

@asyncio.coroutine  # 声明一个协程
def display_date(num, loop):
    end_time = loop.time() + 10.0
    while True:
        print("Loop: {} Time: {}".format(num, datetime.datetime.now()))
        if (loop.time() + 1.0) >= end_time:
            break
        yield from asyncio.sleep(2)  # 阻塞直到协程sleep(2)返回结果
loop = asyncio.get_event_loop()  # 获取一个event_loop
tasks = [display_date(1, loop), display_date(2, loop)]
loop.run_until_complete(asyncio.gather(*tasks))  # "阻塞"直到所有的tasks完成
loop.close()
```

运行结果：

```python
Loop: 2 Time: 2017-10-17 21:19:45.438511
Loop: 1 Time: 2017-10-17 21:19:45.438511
Loop: 2 Time: 2017-10-17 21:19:47.438626
Loop: 1 Time: 2017-10-17 21:19:47.438626
Loop: 2 Time: 2017-10-17 21:19:49.438740
Loop: 1 Time: 2017-10-17 21:19:49.438740
Loop: 2 Time: 2017-10-17 21:19:51.438855
Loop: 1 Time: 2017-10-17 21:19:51.438855
Loop: 2 Time: 2017-10-17 21:19:53.438969
Loop: 1 Time: 2017-10-17 21:19:53.438969
Loop: 2 Time: 2017-10-17 21:19:55.439083
Loop: 1 Time: 2017-10-17 21:19:55.439083
```

## **async 和 await**

Python3.5 中对协程提供了更直接的支持，引入了 `async/await` 关键字。上面的代码可以这样改写：使用 `async` 代替`@asyncio.coroutine`，使用 `await` 代替 `yield from` ，代码变得更加简洁可读。从 Python 设计的角度来说， `async/await` 让协程独立于生成器而存在，不再使用 yield 语法。

```python
import asyncio
import datetime

async def display_date(num, loop):      # 注意这一行的写法
    end_time = loop.time() + 10.0
    while True:
        print("Loop: {} Time: {}".format(num, datetime.datetime.now()))
        if (loop.time() + 1.0) >= end_time:
            break
        await asyncio.sleep(2)  # 阻塞直到协程sleep(2)返回结果

loop = asyncio.get_event_loop()  # 获取一个event_loop
tasks = [display_date(1, loop), display_date(2, loop)]
loop.run_until_complete(asyncio.gather(*tasks))  # "阻塞"直到所有的tasks完成
loop.close()
```

## **asyncio模块**

asyncio 的使用可分三步走：

1. 创建事件循环
2. 指定循环模式并运行
3. 关闭循环

通常我们使用`asyncio.get_event_loop()`方法创建一个循环。

运行循环有两种方法：一是调用 `run_until_complete()` 方法，二是调用 `run_forever()` 方法。`run_until_complete()` 内置`add_done_callback` 回调函数，`run_forever()` 则可以自定义 `add_done_callback()`，具体差异请看下面两个例子。

使用 `run_until_complete()` 方法：

```python
import asyncio

async def func(future):
    await asyncio.sleep(1)
    future.set_result('Future is done!')

if __name__ == '__main__':

    loop = asyncio.get_event_loop()
    future = asyncio.Future()
    asyncio.ensure_future(func(future))
    print(loop.is_running())   # 查看当前状态时循环是否已经启动
    loop.run_until_complete(future)
    print(future.result())
    loop.close()
```

使用 `run_forever()` 方法：

```python
import asyncio

async def func(future):
    await asyncio.sleep(1)
    future.set_result('Future is done!')

def call_result(future):
    print(future.result())
    loop.stop()

if __name__ == '__main__':
    loop = asyncio.get_event_loop()
    future = asyncio.Future()
    asyncio.ensure_future(func(future))
    future.add_done_callback(call_result)        # 注意这行
    try:
        loop.run_forever()
    finally:
        loop.close()
```

## 参考文献

https://www.liujiangblog.com/course/python/83

https://juejin.cn/post/6844903632534503437

https://blog.csdn.net/mieleizhi0522/article/details/82142856