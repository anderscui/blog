---
title: golang-organization
date: 2021-03-14 11:21:06
tags:
    - Go
categories: [Programming, Go]
---

# Go 程序的代码组织

参考：
[How to Write Go Code](https://golang.org/doc/code)
[Organize Local Code](https://stackoverflow.com/questions/55442878/organize-local-code-in-packages-using-go-modules/57314494#57314494)
[Common Project Layout](https://github.com/golang-standards/project-layout)
[Effective Go](https://golang.org/doc/effective_go)

# 代码组织（Code organization）

Go 程序以 `package` 组织。`package` 是一组在同一目录下的源代码文件集合。

一个代码库包含一个或多个 `module`。`module` 是一组相关的 Go `package`。Go 代码通常仅包含一个 `module`，名为 `go.mod` 的文件声明了 module 路径。

`go.mod` 在代码库根目录，其定义的 module 包含了该目录下所有的 package。

module 的路径可作为 import 的前缀，同时也说明了 `go` 命令如何定位并下载之。引用路径是字符串值，package 的引用路径是 module 的子目录，比如：

```bash
github.com/google/go-cmp/cmp
```

但标准库中的 package 不需要 module 路径前缀。

## init

每个源文件可以包含自己的 `init` 函数，以进行必要之设置。`init` 在所属 package 内的所有变量初始化之后（这又在所有引用的 package eval 之后）执行。

`init` 可用于在程序真正执行之前验证和修复程序状态，也可进行配置、初始化变量等等。

所有的 `init` 函数会被编译器发现，并在 `main` 函数之前调用之。

## 理解

对于简单的程序，repository 下只有一个 `module`，通过 `go.mod` 选择一个名称，如果想少打点字，使用 local name，而非完整路径。

在项目内添加不同的目录，其中含有的代码的部分即是 package，这样让代码组织更清晰。package 下可以继续嵌套。

如果要引用本地其它 repository 的 package，使用 `replace`。如果要发布代码，使用完整路径。

# Don't Panic

