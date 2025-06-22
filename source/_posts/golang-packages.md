---
title: golang-packages
date: 2021-03-14 00:12:27
tags:
    - Go
categories: [Programming, Go]
---

# Go 语言中的 Modules 与 Packages

# 创建一个简单 app

[参考：golang-getting-started](https://golang.org/doc/tutorial/getting-started)

## 创建目录

```go
mkdir hello
cd hello
```

## 依赖追踪（dependency tracking）

如果代码需要引入其它模块（module），在 Go 中使用当前项目的 module 来管理。当前的 module 由 `go.mod` 文件定义。`go.mod` 包含在代码库中，与代码同在。

要在当前项目中其中依赖追踪，需运行 `go mod init` 命令。

```go
go mod init andersc.com/hello

// 内容
module andersc.com/hello
go 1.15
```

<!-- more -->

## 编辑代码

```go
package main

import "fmt"

import "rsc.io/quote"

func main() {
	fmt.Println(quote.Go())
}
```

这里定义了一个 `package`，加了若干 `import`，并包含了如何函数 `main()`。

使用如下命令来修改依赖项，它会检查依赖项的修改，并生成 `go.sum` 文件，同时下载必要之文件：

```bash
go mod tidy
```

## 运行 app

```go
go run .
```

以上是最简单的 app，有第三方库的引用，但 app 自身结构及其简单。

# 创建一个略复杂的 app

Go 程序的代码首先分组为 packages，packages 再分组为 modules。

[参考：golang-create-module](https://golang.org/doc/tutorial/create-module)

## 创建一个可复用的 module

```bash
mkdir greetings
cd greetings

go mod init andersc.com/greetings
```

创建并编辑 `greetings.go`：

```go
package greetings

import "fmt"

func Hello(name string) string {
	// Return a greeting that embeds the name in a msg.
	msg := fmt.Sprintf("Hi, %v. Welcome!", name)
	return msg
}
```

这样，一个简单的 module 和 package 就完成了，并且可被其它 module 引用。

## 在另一 module 中调用

```bash
cd ..
mkdir hello
cd hello
go mod init andersc.com/hello
```

入口程序：

```go
package main

import (
	"fmt"

	"andersc.com/greetings"
)

func main() {
	// Get a greeting msg and print it.
	msg := greetings.Hello("Gladys")
	fmt.Println(msg)
}
```

由于 `greetings` module 在本地，需要修改 `go.mod`：

```bash
go mod edit -replace=andersc.com/greetings=../greetings

go mod tidy

go run . # Hi, Gladys. Welcome!
```

至此，一个“完整的” app 搭建完毕。


