---
title: effective-go
date: 2021-03-14 14:37:54
tags:
    - Go
categories:
    - Go
---

# Effective Go

[Effective Go](https://golang.org/doc/effective_go)

# Errors

`error` 的信息可以通过前缀来增加信息，如 `image: unknown format`。

## Panic

向调用者报告错误通常的方法是返回一个 `error`，但有时候一个错误发生，意味着严重的或很难预料到的错误（比如本应是无限循环，却退出了），此时退出程序也许更为合适，这是 `panic` 的用场。

通常来说，应避免使用 `panic`，但凡程序还有救的话。一些特殊情况是，程序启动时无法连接到主数据库，那么继续执行下去也无意义，此时可以 `panic`。

## Recover

在 `panic` 调用后（包括自定义 `panic`，以及 slice 索引越界等运行时错误），当前函数执行立即结束，开始释放 goroutines，执行 defferred 函数。如果释放过程到了程序的 top level 代码，程序就终止了。

但还是可以通过 `recover` 来重新获得 goroutine 的掌控，继续执行程序。

`recover` 停止上述释放过程，返回 `panic` 的参数，`recover` 只能用于 deferred 函数。