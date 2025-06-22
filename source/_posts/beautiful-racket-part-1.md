---
title: beautiful-racket-part-1
date: 2020-10-03 16:25:00
tags:
  - FP
  - Racket

categories: 
  - [FP, Racket]
---

# Foreword by Matthew Flatt

让我们回顾1993年时的事情：

* 为 GC 是否可作为手动内存管理的替代项而争论过（当时还没有 Java）
* 为一等函数和闭包是否能被平均水平的程序员掌握而讨论过（当时还没有 JavaScript）
* 我们终于确认了，静态类型系统辅以良好的保证确实物有所值（此时大部分程序员没有听说过 Haskell 和 ML，在 Java 引入泛型的几年之前）

此类技术，眼下已经属于理所应当，而彼时却会引发争论。当然，这些变化不是在一夜之间发生的。部分原因是，硬件性能和软件的scale的变化，改变了编程语言的可能性与tradeoff。部分原因是，越来越多的人尝试不同技术，然后不愿再回去了。

本书的核心是语言的可扩展性，对 Racket 来说是其中的 Macro。那么，如果宏真地这么好，为什么不是每个人都已在使用？如同 GC，宏看起来很酷，但也引入了太多 overhead（不是程序执行上，而是程序理解上）。类似于一等函数，宏在代码中引入了另一维度。

PS：应该不类似于 GC，因为GC实际上是大大减轻了程序员的心智负担。

<!-- more -->

# Make a language in one hour: stacker

## Why Make Languages

### What is a programming language?

不管是编写一般的程序，还是特殊的 compiler 与 interpreter，它们都遵循一个统一的模式：

* take some input -> put it into a processing device -> get a result

中间的部分 device 可大可小，可以说一个函数、程序，也可以是一种编程语言。以如此抽象的视角去看待，那么编程语言可以视为特殊的函数，一个函数可以视为一种 DSL（在一个极小的领域内）。

### How are languages implemented in Racket?

分为三步：

* 设计语言的 notation 和 behavior
* 编写一个 Racket 程序来读入源代码，将其 notation 和 behavior 转换为相应的 Racket 程序
* 执行转换后的 Racket 程序

第三步整个地由 Racket toolchain 完成，因此只需要关注前两部。

### The Components of a Language

* reader：将源代码从字符串转换为 Racket 的 S 表达式。
* expander：确定这些表达式如何对应到可执行的 Racket 表达式（从而可进一步产生结果）

reader 负责将程序转换为正确的形式；expander 负责给这些形式赋予意义。

奇妙的是，我们甚至可以先将 reader 设计为生成不存在的函数，之后再在 expander 中实现。

## The Reader

```racket
; stacker.rkt
#lang br/quicklang

(define (read-syntax path port)
  (define src-lines (port->lines port))
  (datum->syntax #f '(module lucy br
                      "Hello World")))

(provide read-syntax)
```

每个 reader 都需要提供一个 `read-syntax` 函数，Racket 给 reader 传入两个参数：path 与 port。

每一个 `read-syntax` 都需要做一件事情：返回代码，该代码描述了一个称为 `syntax object` 的模块。Racket 将源代码替换为此模块。新模块将调用 expander，从而将模块完全扩展。此后，模块将被 Racket 解释器正常求值。

### reader 示例解释

`read-syntax` 函数做两件事情，一是从 port 中读取源代码，二是将结果转为上述的模块（模块也是表达式）。模块的模式是：

```racket
; 此表达式包含需要 expand 和 evaluate 的表达式
(module module-name which-expander
    42
    "foobar"
    (+ 1 1)
    ...)
```

示例中，先将代码表示为 data（quote），在用 `datum-syntax` 将其转换为 `syntax object`。

### 测试

现在可以测试所编写的 reader 代码。

```racket
; stacker-test.rkt
#lang reader "stacker.rkt"

foo
bar
```

在执行时，这段代码将会被完全替换为 reader 返回的结果，因此打印出：`Hello World`。

一般地，每一个以 `#lang` 开头的文件都会被如此转换为另一个模块。

### 改进

上面的Hello World什么也不错，实际上是完全忽略了输入，下面来让它做点事情。

```racket
#lang br/quicklang

(define (read-syntax path port)
  (define src-lines (port->lines port))
  (define src-datums (format-datums '(handle ~a) src-lines))
  (define module-datum `(module stacker-mod "stacker.rkt"
                          ,@src-datums))
  (datum->syntax #f module-datum))

(provide read-syntax)
```

* `format-datums` 接受字符串列表，对每个元素进行格式化。
* 先是表示 quasiquote ，`,@` 则读取列表

用如下代码测试：

```racket
#lang reader "stacker.rkt"
4
8
+
3
*
```

转换的模块内容是：

```
'(handle)
'(handle 4)
'(handle 8)
'(handle +)
'(handle 3)
'(handle *)
```


