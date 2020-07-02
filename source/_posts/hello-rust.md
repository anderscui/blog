---
title: 'Hello, Rust'
date: 2017-09-24 23:06:09
tags:
	- Programming
	- Rust
---

# 安装

```
# On Mac
$ curl https://sh.rustup.rs -sSf | sh
```

运行此命令，按其中的提示安装即可。

## 升级与卸载

```
$ rustup update

$ rustup self uninstall
```

## 确认

```
$ rustc --version
```

## 本地文档

```
# 使用此命令在浏览器中打开本地文档，其中包含 The Rust Programming Language.
$rustup doc
```

# Hello, World!

假设把代码放在某个目录下，cd到它，创建一个`main.rs`文件（rs是Rust文件的扩展名）：

```rust
fn main() {
    println!("Hello, world!");
}
```

然后，编译再运行之：

```
$ rustc main.rs
$ ./main
```

## Rust程序的结构

`fn`定义一个函数，而`main`同时是特别的**入口函数**，与`C`和`C#`之类语言类似。Rust中的函数需要花括号包含其函数体。

第二行`println!("Hello, world!");`，输出一行文本到屏幕。这里看起来有点特别的是`println!`，带有`!`在Rust中意味着调用宏而非函数。行末的`;`表示一个表达式的结束，**大多数**Rust代码行以`;`结束。

# 编译和执行的分离

作为静态语言，Rust程序执行之前需要先行编译，这类似于`gcc`或`clang`。

对于简单的程序，可如上使用`rustc`，对于更为复杂的程序，你可能需要`Cargo`这个工具。

# Cargo

`Cargo`是Rust的构建系统（Build System）和包管理器（Package Manager）。它可以生成代码、下载依赖库，生成库代码。

`Cargo`随Rust一起安装。

## 以Cargo创建一个项目

使用命令

```
$ cargo new hello_cargo --bin
```

创建一个项目，`--bin`表示所创建者为可执行文件（binaries）。`Cargo`生成了两个文件：

* Cargo.toml（TOML格式的配置文件）
* src/main.rs（与上面手工创建的一样）

Cargo.toml内容如下：

```toml
[package]
name = "hello_cargo"
version = "0.1.0"
authors = ["andersc <andersc@mail.com>"]

[dependencies]
```

`[dependencies]`列出了依赖的库，这些依赖在Rust中称为`crate`。Cargo创建了初始的目录结构，也是建议将代码放在src下，其它文件放在顶级目录。

## Building and Running a Cargo Project

使用如下命令编译并运行程序：

```
$ cargo build
$ ./target/debug/hello_cargo
```

首次执行`cargo build`时，它会创建文件`Cargo.lock`，用于跟踪应用的依赖。一般不需要手工改动之。作为一种更快捷的方式，可以用`cargo run`来编译并运行程序。

## 发布程序

上面可见，编译的程序放在了`debug`下，如果要正式发布，则应使用`cargo build --release`选项，这会优化所生成的程序，代价是编译时间更长。

# 开发环境

* Sublime 3：[Sublime Rust](https://github.com/rust-lang/sublime-rust)
* [IntelliJ Rust](https://intellij-rust.github.io/)

# Guessing Game

通过一个真实的程序来演示几个Rust的常见概念，如`let`， `match`，方法，关联函数，使用外部库等。

猜数游戏是一个经典的编程初学者的问题。

## 建立项目

```shell
$ cargo new guessing_game --bin
$ cd guessing_game
```

## 感受一下

```rust
use std::io;

fn main() {
    println!("Guess the number!");
    println!("Please input your guess.");

    let mut guess = String::new();
    io::stdin().read_line(&mut guess)
               .expect("Failed to read line");

    println!("You guessed: {}", guess);
}
```

这里面，`use`不用多说；let创建一个**变量**，不过在Rust中变量默认是不可修改的，如果希望修改，则需要使用**mut**；String:new()就是所谓关联函数（associated function），相当于其它语言中的**静态方法**。

`stdin`是io的关联函数；read_line函数读取用户输入，将值赋给一个变量，`&`表示参数为**引用（reference）**。


