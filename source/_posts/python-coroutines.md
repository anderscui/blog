---
title: Python中的协程
date: 2019-05-12 02:53:25
tags:
  - Python

categories: [Programming, Python]
---

本文主要整理自《Fluent Python》的第16章。

David Beazley（又）尝言，协程是py文档中最语焉不详、模糊的，看起来是一个毫无用处的特性。当然，事实并非如此，他写过关于协程与并发的系列文章：[A Curious Course on Coroutines and Concurrency](http://www.dabeaz.com/coroutines/)。

`yield`一次在英语中有两个主要含义：产生和让路。在生成器中，这两个含义都适用：每次产生一个值，生成器都会挂起，“让路”给调用者。

协程的语法看起来像是生成器，但协程一般出现在表达式的右边（如`datum = yield`），它也不一定需要产生一个值。调用者使用`.send()`时，协程接受到一个值。

甚至可以完全没有数据从`yield`进出，所以不妨把`yield`看作**一个流程控制装置**，可用于实现协作式多任务处理：每个协程交出控制给central scheduler，随后其它协程可被激活。

# 协程的基本操作

```python
from inspect import getgeneratorstate


def simple_coro(a):
    print('-> Started: a =', a)
    b = yield a
    print('-> Received: b =', b)
    c = yield a + b
    print('-> Received: c =', c)


coro = simple_coro(14)
print(getgeneratorstate(coro))
print(next(coro))
print(getgeneratorstate(coro))
print(coro.send(28))
print(coro.send(99))
```

从调用者角度看，它获取数据的方式与生成器类似，但是它同时多了两次`send`操作，`send`比`next`多了一个方向的数据流转，但对于`yield`，它依然会挂起当前例程（此处为协程），将执行交给调用者。

# 协程示例：持续计算均值

```python
def averager():
    total, count = 0, 0
    average = None
    while True:
        term = yield average
        total += term
        count += 1
        average = total / count


co = averager()
print(next(co))
print(co.send(10))
print(co.send(30))
print(co.send(5))
```

实现这一功能不是非得用协程，也可以用类或闭包，但在协程里，total和count都是简单的局部变量。

在上面两个例子中可以看到，使用协程都需要最开始调用一次`next`，这次操作一般称为“启动”（prime）。每个协程都需要启动，而协程也是函数，所以装饰器就派上用场了。

# 启动协程的装饰器

```python
from functools import wraps


def coroutine(func):
    @wraps(func)
    def primer(*args, **kwargs):
        gen = func(*args, **kwargs)
        next(gen)
        return gen
    return primer
```

# 协程的终止与异常处理

`.throw()`与`.close()`。

# 协程的返回值

协程是生成器函数，因此可以有返回值，此时协程必须是正常终止的，比如`while`break之后。

