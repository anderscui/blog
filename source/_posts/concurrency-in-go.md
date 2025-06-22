---
title: concurrency-in-go
date: 2021-03-22 01:05:26
tags:
    - Go
categories: [Programming, Go]
---

# Concurrency in Go

参考：《Go in Action》第六章

* 使用 `goroutine` 运行代码
* 检测和修复 race condition
* 在 channel 之间共享数据

Go 运行时 scheduler 能够有效地管理所有 goroutines。scheduler 在 OS 之上，将 OS 的线程绑定到 logical 处理器，后者执行 goroutine。

并发同步源于 CSP 范式，CSP 是一个消息传输模型。它通过在 goroutine 之间的数据通信来同步数据，而非 lock。在 Go 中，消息传输的核心数据类型是 `channel`。

## 并发与并行

OS 调度线程，使之执行于物理处理器上，Go 运行时调度 goroutines，使之执行于逻辑处理器上。每个逻辑处理器则独立地绑定在单个 OS 线程上。

## Goroutines

## Race Condition

```bash
go build -race
```

## Locking shared resources

