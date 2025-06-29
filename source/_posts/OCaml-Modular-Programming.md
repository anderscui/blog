---
title: OCaml Modular Programming
date: 2025-06-26 06:50:50
tags:
  - Programming
  - OCaml
categories: [Programming, OCaml]
---

> 按：本文整理自[《OCaml Programming: Correct + Efficient + Beautiful》](https://cs3110.github.io/textbook/cover.html)一书的第五章 Modular Programming。
>
> 初学 OCaml 时，建议先看完《OCaml from the Very Beginning》，然后看《Learn Programming with OCaml》的前三（或五）章，然后再来看《OCaml Programming》第五章。再往后，就按需去看吧。

在程序规模很小的时候，如数十行，它完全可以由一个人完成，代码放在单个文件中。但在真实世界的开发中，程序规模要大好几个数量级，多的能到数千万行。此时，它必须有多个程序员完成，并且代码放在不同文件中。

其中一个解决方案是**模块化编程（modular programming）**：程序由多个分别开发的模块构成。划分为模块后，每个程序员都仅关注于手头的任务，无须理解整个程序。

同时，我们需要确保每个模块的准确性，此时使用的是 local reasoning：不需要太多关注整体的程序，而是关注它需要满足的 **contract**（此为**接口**）。如果每个人都能正确完成，那么各个分别开发的程序组合起来之后应当可以正确工作。这是模块化编程的核心思想。

因此，构建大型程序必须要使用 **abstraction**，即上面所提及的 contract、接口。不同组件之间通过 abstraction 连接，而非**实现的细节**。

模块通过给出它们应当完成的功能（specification，规格 or 规范）进行抽象。好的模块规格应当是清晰易懂的，提供恰到好处的信息，使得该模块的 client 能正确使用之。

<!-- more -->

上述模块规范（specification）称为**接口（interface）**，它向 client 提供功能信息，并隐藏具体实现。在 Java 中，interface 显然是一种 interface，广义上讲，一个类的 public 成员也提供了一种 interface。

在日常开发中，大部分开发者已经习惯了作为模块使用者去开发：标准库由若干模块组成，开发者根据模块的接口文档去使用之，但通常不去考虑实现细节。在这个意义上，接口如同一份 contract（契约）。

设计接口时，需要注意：

* 最好让实现者和 client 共同参与，达成共识
* 虽然开发的进行，修改代码会越来越困难
* **模块应该仅通过其声明的接口使用**

模块化编程的好处：

* 可以分别开发：其中重要的是 interface，相当于约定，如此不同开发者可专注于不同模块（功能）的开发
* 可单独理解：每个模块自成一体，比如关于 collections、datetime 等的模块
* reason locally, not globally

## 5.1 Module Systems

编程语言的模块系统（Module System），是它用来支持模块化编程的一组特性。

Java 与 OCaml modularity 的对比：

![](/images/ocaml/features_for_modularity.png)

以下是模块系统常见的 concerns：

* **Namespace**：一个命名空间提供了逻辑上相关的一组名称，并与其他命名空间相区别。对于模块化来说，命名空间是必要的，否则容易有命名冲突的情形。Java 使用 package、class 来对名称分组，OCaml 的 structure 类似于 class。structure 是 OCaml 模块系统的 core。
* **Abstraction**：抽象，意味着公开一些信息，而隐藏另一些信息。故而抽象使得封装成为可能。抽象对于模块化也是必要的，它使得实现者隐藏实现细节，避免 client 滥用细节；同时也使得 client 免于面对过多细节。
  * Java 中，interface 和 abstract class 提供抽象能力。
  * OCaml 中则是使用 signatures。
* Code reuse：代码复用是指，模块系统可以让开发者引入一个模块的部分代码而不需要 copy 之。
  * Java 中，使用 subtyping 和 inheritance
  * OCaml 中，使用 functors、includes。functor 类似于 function，它从旧的模块中生出新模块；include 则像是智能形式的 copy-paste。

## 5.2 Modules

一个 structure，只是一组（collection）定义，比如：

```ocaml
struct
  let inc x = x + 1
  type primary_color = Red | Green | Blue
  exception Oops
end
```

但它不能编译，但可以将其 bind 给一个name：

```ocaml
module MyModule = struct
  let inc x = x + 1
  type primary_color = Red | Green | Blue
  exception Oops
end
```

在 utop 中显示：

```bash
module MyModule :
  sig
    val inc : int -> int
    type primary_color = Red | Green | Blue
    exception Oops
  end
```

这说明 `MyModule` 已经被定义，类型为 `module type` ，而 type 的 signature 则是 `sig...end` 部分。

PS：为何说它是 signature，可以看 inc 函数，它仅作为签名出现。

PPS：signature 本身是一组 specification。

### 更真实的例子

```ocaml
module ListStack = struct
  (** [empty] is the empty stack. *)
  let empty = []

  (** [is_empty s] is whether [s] is empty. *)
  let is_empty = function [] -> true | _ -> false

  (** [push x s] pushes [x] onto the top of [s]. *)
  let push x s = x :: s

  (** [Empty] is raised when an operation cannot be applied
      to an empty stack. *)
  exception Empty

  (** [peek s] is the top element of [s].
      Raises [Empty] if [s] is empty. *)
  let peek = function
    | [] -> raise Empty
    | x :: _ -> x

  (** [pop s] is all but the top element of [s].
      Raises [Empty] if [s] is empty. *)
  let pop = function
    | [] -> raise Empty
    | _ :: s -> s
end
```

这里是使用 List 实现一个 Stack，signature 是：

```ocaml
module ListStack :
  sig
    val empty : 'a list
    val is_empty : 'a list -> bool
    val push : 'a -> 'a list -> 'a list
    exception Empty
    val peek : 'a list -> 'a
    val pop : 'a list -> 'a list
  end
```

可以看到，这里的 module 定义和 signature 就和 Java 等语言中的 interface、class 很接近了。而且 ListStack 的内容是高内聚的，即都与 stack of list 高度相关，这是 module 应有之义。

使用例子：

```ocaml
ListStack.push 2 (ListStack.push 1 ListStack.empty)

- : int list = [2; 1]
```

上面一行代码可以简化为：

```ocaml
# ListStack.(push 2 (push 1 empty));;
- : int list = [2; 1]
```

使用 `ListStack.(e)` 时，ListStack 中的所有name在e中都是可用的。通过 pipeline 可进一步简化之：

```ocaml
# ListStack.(empty |> push 1 |> push 2);;
- : int list = [2; 1]
```

相比于 Java 中的 class，这里的 module 要简陋许多，因为 module 只是一组**定义**的容器而已。

### Module Definitions

`module` 定义关键词，与 `let` 很像，差异在于：

* let：将一个 value 绑定到 name
* module：将一个 module value 绑定到 name

基本语法：

```ocaml
module ModuleName = struct
  module_items
end
```

模块内的 module_items，可以是 let、type 定义、exception，也可以嵌套的其他 module 定义。模块名需要以大写字母开头，一般使用 `CamelCase`。

PS：毕竟是 OCaml，怎么能不使用 CamelCase 呢？

但更准确的形式应是：

```ocaml
module ModuleName = module_expression
```

其中 struct 只是形式之一，另一种是：

```ocaml
module L = List
```

这相当于是为既有模块使用一个 alias。

structure 中的定义，可以选择使用 `;;` 分隔，像 toplevel 中那样，但并非必要：

```ocaml
module M = struct
  let x = 0;;
  type t = int;;
end
```

### Scope and Open

如果有以下 module 定义：

```ocaml
module M = struct let x = 42 end
```

要访问其中的 x，可使用 `M.x`，也可以使用 open：

`open M`，这样可直接使用 `x` 访问之了。

PS：`open module` 语句，其作用相当于将模块中的定义在当前作用域内“写一遍”。比如 `open String`，相当于：

```ocaml
let length = String.length
let get = String.get
let lowercase_ascii = String.lowercase_ascii
...
```

在当前 scope 内有了 length、get 等函数的定义。

总是 open 的 module，即标准库 **Stdlib**。

open 语句也可以作为一种 module_items：

```ocaml
module M = struct
  open List

  (** [uppercase_all lst] upper-cases all the elements of [lst]. *)
  let uppercase_all = map String.uppercase_ascii
end
```

由于 `open List` ，map 函数可直接使用。如果也 open String，那么会导致一个问题，map 有冲突，后 open 者会覆盖先 open 者。

有一个可选的、有些怪异的方式：`let open M in e`：

```ocaml
(** [lower_trim s] is [s] in lower case with whitespace removed. *)
let lower_trim s =
  let open String in
  s |> trim |> lowercase_ascii
```

### Module Type Definitions

在如下的定义中：

```ocaml
module ModuleName = struct
  module_items
end
```

OCaml 会 infer 出 module 的 type，它是一个 signature（就像函数的 type 也是一个 signature）。而这个签名，实际上也可以自己写出：

```ocaml
module type LIST_STACK = sig
  (** [Empty] is raised when an operation cannot be applied
      to an empty stack. *)
  exception Empty

  (** [empty] is the empty stack. *)
  val empty : 'a list

  (** [is_empty s] is whether [s] is empty. *)
  val is_empty : 'a list -> bool

  (** [push x s] pushes [x] onto the top of [s]. *)
  val push : 'a -> 'a list -> 'a list

  (** [peek s] is the top element of [s].
      Raises [Empty] if [s] is empty. *)
  val peek : 'a list -> 'a

  (** [pop s] is all but the top element of [s].
      Raises [Empty] if [s] is empty. *)
  val pop : 'a list -> 'a list
end

module ListStack = struct
  let empty = []

  let is_empty = function [] -> true | _ -> false

  let push x s = x :: s

  exception Empty

  let peek = function
    | [] -> raise Empty
    | x :: _ -> x

  let pop = function
    | [] -> raise Empty
    | _ :: s -> s
end
```

这里有几个值得注意的点：

* `module type LIST_STACK = sig end`：这是 **module type** 的**签名**定义，定义了 interface，并且 注释也包含在此处，这是合理的，因为 signature 才是接口，而不是 structure
* `module ListStack = struct end`：这是 **module** 的定义。

不过此时，两者尚没有任何关联，要关联起来，可添加 **type annotation**：`module ListStack : LIST_STACK = struct ... end`，此时在 utop 中加载时，显示会很不同：

```bash
#use "stacks.ml";;
module type LIST_STACK =
  sig
    exception Empty
    val empty : 'a list
    val is_empty : 'a list -> bool
    val push : 'a -> 'a list -> 'a list
    val peek : 'a list -> 'a
    val pop : 'a list -> 'a list
  end
module ListStack : LIST_STACK
```

#### 更多语法

关于 type annotations，其语法是：

```ocaml
module ModuleName : module_type = module_expression

(* 以下为示例 *)
module ListStackAlias : LIST_STACK = ListStack
(* equivalently *)
module ListStackAlias = (ListStack : LIST_STACK)

module M : sig val x : int end = struct let x = 42 end
(* equivalently *)
module M = (struct let x = 42 end : sig val x : int end)
```

在上面四个示例中：

* 第一、二：是 Module 的别名
* 第三：是将 type annotation 加在了 module name 之后
* 第四：类似于 `(e: t)` t在此处作为手动添加的类型标注

```ocaml
(* 示例三 *)
module M : sig 
  val x : int 
end = struct 
  let x = 42 
end

(* 示例四 *)
module M = (struct let x = 42 end : sig val x : int end)
```

### Module Type Semantics

如上所述，如果 M 只是一个 struct block，那么其 type 可由编译器自动推断，但这一点可通过 annotation 改变。那么问题是：`module M: T = ...` 究竟意味着什么？编译器可保证两点：

* signature matching：T 的每个 name 在 M 都有定义，类型要么相同，要么更 general（即 T 被完全涵盖）
* opacity：出现于 M 中，但不在 T 中的名字，对于外部都是不可见的

这一点看起来很像是 Java 中的 **subtyping**：

```java
class C { }
class D extends C { }

D d = new D();
C c = d;
```

最后一行可见，D 类型的值可以赋值给 C 类型。（还可以想起很久以前学过的 Liskov Principle 吗？）

```ocaml
module M = struct
  let x = 0
  let z = 2
end

module type X = sig
  val x : int
end

module MX = (M : X)

module type Z = sig
  val z : int
end

module MZ = (M : Z)
```

看上面 OCaml 的例子。模块 M 有 x、z 两成员，`module MX = (M : X)` 相当于将 M 类型的值赋值给 X，此时 MX 模块的 public 成员只有 X 包含的，即 x；类似地，MZ 只有 z 可以访问。

从什么可以看到，在 OCaml 中将 M 标记为 X 类型，并不需要两者有显式声明的关系（如 extends 或 implements）。这两种方式分别称为：

* nominal subtyping：名义上的子类型，明确声明
* structural subtyping：结构上的，结构上兼容即可（再如 py 中的 duck type 也是）

一种特殊情况：

```ocaml
module MXZ' = ((M : X) : Z)
```

对于 (M: X)，其结果的类型是 X，此时 (X: Z) 就会报错，虽然最初的 (M: Z) 是有效的。

这是 OCaml 静态类型系统的一个结果，看起来似乎不够灵活，但也减少了许多运行时错误。

## 5.3 Modules and the Toplevel

### 一个小例子

`mods.ml` 的内容如下：

```ocaml
let b = "bigred"
let inc x = x + 1
module M = struct
  let y = 42
end
```

使用 `ocamlc mods.ml` 编译之，会得到三个文件：

* mods.cmo：compiled module object file，即 bytecode
  * `ocamlc -c foo.ml`
* mods.cmi：compiled module interface，模块接口信息
  * 从 mli 生成，或从 ml 推断而得
  * `ocamlc -c foo.mli`
* a.out：可执行文件

在 utop 中可以 load 该模块：`#load "mods.cmo"`，其效果如同将 ml 代码在 utop中输入一样。

* Mods.b：有效
* Mods.M.y：有效
* inc：无效

可使用 `open Mods;;` 将所有成员一起引入。

### 使用 dune utop

```ocaml
# 目录结构
project/
├── lib/
│   ├── mods.ml
│   └── dune

# dune 内容
(library
 (name mods))
 
# 启动 utop
dune utop lib

# 此时 Mods 直接可用
```

### utop 的 init 文件

使用全部或当前目录的 `.ocamlinit` 文件，作用很类似于 ipython 的 `~/.ipython/profile_default/startup/start.py` 文件。

### Load vs. Use

`#load` 是加载一个编译过的 cmo 文件；而 `#use` 是使用一个 source code 文件。使用起来差别也很明显，后者相当于是将文件内容直接输入到 utop，因此以 `mods.ml` 为例，其结果不是引入了 Mods 模块，而是使得 Mods 下的各个成员直接可见了。

一般来说，推荐使用 `#load`，因为它准确反映了模块与其他代码是如何真实交互的。

## 5.4 Encapsulation

模块系统的主要关注点之一便是封装：将实现细节隐藏于接口之后。

### Opacity

在任何一种语言中，以下的封装场景都是很常见的：在一个模块中想要提供函数，同时希望它的辅助函数不可见。比如：

```ocaml
module Math = struct
  (** [fact_aux n acc] is [n! * acc]. *)
  let rec fact_aux n acc =
    if n = 0 then acc else fact_aux (n - 1) (n * acc)

  (** [fact n] is [n!]. *)
  let fact n = fact_aux n 1
end
```

一种方案是将 fact_aux 作为 fact 的嵌套函数，但有时这样不太好。更好地做法是使用 module type：

```ocaml
module type MATH = sig
  (** [fact n] is [n!]. *)
  val fact : int -> int
end

module Math : MATH = struct
  (** [fact_aux n acc] is [n! * acc]. *)
  let rec fact_aux n acc =
    if n = 0 then acc else fact_aux (n - 1) (n * acc)

  let fact n = fact_aux n 1
end
```

添加这样的 annotation 之后，Math 只能作为 MATH interface 来使用。

在这个意义上，可以说 type annotation 是不透明的（opaque），也可以说它把 module 密封（seal）了。

如果希望做定义的检查，同时又不隐藏任何内容，该怎么做呢？

```ocaml
module type MATH = sig
  (** [fact n] is [n!]. *)
  val fact : int -> int
end

module Math = struct
  (** [fact_aux n acc] is [n! * acc]. *)
  let rec fact_aux n acc =
    if n = 0 then acc else fact_aux (n - 1) (n * acc)

  let fact n = fact_aux n 1
end

module MathCheck : MATH = Math
```

定义 Math 的时候不使用 annotation，但之后再添加用于检查的定义，但这样的名字具体为何毫无意义，所以也可以：

```ocaml
module _ : MATH = Math
```

### 与 visibility modifier 的比较

```java
class C {
  private int x;
  public int y;
}

module type C_PUBLIC = sig
  val y : int
end

module CPrivate = struct
  let x = 0
  let y = 0
end

module C : C_PUBLIC = CPrivate
```

在这个例子中，Java 代码大致可在 OCaml 中如此表示。

### Abstract Types

```ocaml
module type LIST_STACK = sig
  (** [Empty] is raised when an operation cannot be applied
      to an empty stack. *)
  exception Empty

  (** [empty] is the empty stack. *)
  val empty : 'a list

  (** [is_empty s] is whether [s] is empty. *)
  val is_empty : 'a list -> bool

  (** [push x s] pushes [x] onto the top of [s]. *)
  val push : 'a -> 'a list -> 'a list

  (** [peek s] is the top element of [s].
      Raises [Empty] if [s] is empty. *)
  val peek : 'a list -> 'a

  (** [pop s] is all but the top element of [s].
      Raises [Empty] if [s] is empty. *)
  val pop : 'a list -> 'a list
end

module ListStack : LIST_STACK = struct
  exception Empty
  let empty = []
  let is_empty = function [] -> true | _ -> false
  let push x s = x :: s
  let peek = function [] -> raise Empty | x :: _ -> x
  let pop = function [] -> raise Empty | _ :: s -> s
end
```

signature 为：

```ocaml
module type LIST_STACK =
  sig
    exception Empty
    val empty : 'a list
    val is_empty : 'a list -> bool
    val push : 'a -> 'a list -> 'a list
    val peek : 'a list -> 'a
    val pop : 'a list -> 'a list
  end
```

如果因为某个功能的需要，要将底层的 `'a list` 替换，比如替换为 `'a list * int`，即其底层实现是通过一个 list 和一个表示 size 的 int 完成的。那么上面的 signature 必将大改，而且所有 client 代码亦然。

但从抽象角度看，无论是 `'a list` 还是 `'a list * int`，都可以理解为用于实现 List 的底层类型，这无疑也是可以抽象出来的，如此，也不许因为新类型而大量改变接口设计。因此可以修改为：

```ocaml
module type LIST_STACK = sig
  type 'a stack
  exception Empty
  val empty : 'a stack
  val is_empty : 'a stack -> bool
  val push : 'a -> 'a stack -> 'a stack
  val peek : 'a stack -> 'a
  val pop : 'a stack -> 'a stack
  val size : 'a stack -> int
end

module ListStack : LIST_STACK = struct
  type 'a stack = 'a list
  exception Empty
  let empty = []
  let is_empty = function [] -> true | _ -> false
  let push x s = x :: s
  let peek = function [] -> raise Empty | x :: _ -> x
  let pop = function [] -> raise Empty | _ :: s -> s
  let size = List.length
end

module ListStackCachedSize : LIST_STACK = struct
  type 'a stack = 'a list * int
  exception Empty
  let empty = ([], 0)
  let is_empty = function ([], _) -> true | _ -> false
  let push x (stack, size) = (x :: stack, size + 1)
  let peek = function ([], _) -> raise Empty | (x :: _, _) -> x
  let pop = function
    | ([], _) -> raise Empty
    | (_ :: stack, size) -> (stack, size - 1)
  let size = snd
end
```

在实现module中，分别有一行指定了当前实现对应的 abstract type 是什么：

* `type 'a stack = 'a list`
* `type 'a stack = 'a list * int`

它甚至还可以像下面这样指定类型：

```ocaml
type 'a entry = {top : 'a; rest : 'a stack; size : int}
and 'a stack = S of 'a entry option
```

而有了各种各样的实现之后，`LIST_STACK` 改为 `Stack` 更为合适：

```ocaml
module type Stack = sig
  type 'a stack
  exception Empty
  val empty : 'a stack
  val is_empty : 'a stack -> bool
  val push : 'a -> 'a stack -> 'a stack
  val peek : 'a stack -> 'a
  val pop : 'a stack -> 'a stack
  val size : 'a stack -> int
end

module ListStack : Stack = struct
  type 'a stack = 'a list
  exception Empty
  let empty = []
  let is_empty = function [] -> true | _ -> false
  let push x s = x :: s
  let peek = function [] -> raise Empty | x :: _ -> x
  let pop = function [] -> raise Empty | _ :: s -> s
  let size = List.length
end
```

对于 `ListStack.empty`，其签名是：

```ocaml
# ListStack.empty;;
- : 'a ListStack.stack = <abstr>
```

这一类型名略显笨重冗余，OCaml 的习惯做法是使用 `t` 表示之。

```ocaml
module type Stack = sig
  type 'a t
  exception Empty
  val empty : 'a t
  val is_empty : 'a t -> bool
  val push : 'a -> 'a t -> 'a t
  val peek : 'a t -> 'a
  val pop : 'a t -> 'a t
  val size : 'a t -> int
end

module ListStack : Stack = struct
  type 'a t = 'a list
  exception Empty
  let empty = []
  let is_empty = function [] -> true | _ -> false
  let push x s = x :: s
  let peek = function [] -> raise Empty | x :: _ -> x
  let pop = function [] -> raise Empty | _ :: s -> s
  let size = List.length
end
```

形如 `Stack.t` 的表示在标准库中也是很常见的。

严格来说，在 signature 中实际上也可以暴露具体类型：

```ocaml
module type T = sig
  type t = int
  val x : t
end

module M : T = struct
  type t = int
  let x = 42
end

let a : int = M.x
```

由于在 sig 中，t = int，因此 a 的类型确实为 int，但如果保持 abstract，那么唯一能确定的只是 `M.t`。

## 5.5 Compilation Units

OCaml 中的一个编译单元，是指同一目录下的**一对儿源文件**：它们名称相同，但扩展名分别是 `.ml` 和 `.mli`，前者是 implementation，后者是 interface。

如果有一对儿文件 foo.mli 和 foo.ml，文件内容分别是：

```ocaml
val x : int
val f : int -> int

let x = 0
let y = 12
let f x = x + y

```

则其效果等价于在 foo.ml 中手动定义一个 module：

```ocaml
module Foo : sig
  val x : int
  val f : int -> int
end = struct
  let x = 0
  let y = 12
  let f x = x + y
end
```

一般来说，当编译器遇到一个编译单元，它就是如上处理的：

```ocaml
module Foo
  : sig (* insert contents of foo.mli here *) end
= struct
  (* insert contents of foo.ml here *)
end
```

标准库使用编译单元实现了我们在使用的大部分模块。

### 文档注释

* 一种 abstraction 的 client，理应去阅读接口文件（或以此产生的文档），因此编写接口文件的注释时，应当以 client 为目标读者。它们应当描述清楚：如何使用抽象、前置条件、可能的异常、使用了哪些重要算法。
* 不应期望 client 去阅读实现文件，它们主要是创建者和维护者阅读的。实现文件的注释应当描述实现的内部细节，如表示类型如何使用、代码如何工作、重要的内部 invariant 等。

文档不应在 interface 和 implementation 两个文件中有重复。总的来说，interface 的文档更重要。

### Stack 的一个例子

使用了文件 `mystack.mli` 和 `mystack.ml`：

```ocaml
(* .mli *)
type 'a t
exception Empty
val empty : 'a t
val is_empty : 'a t -> bool
val push : 'a -> 'a t -> 'a t
val peek : 'a t -> 'a
val pop : 'a t -> 'a t

(* .ml *)
type 'a t = 'a list
exception Empty
let empty = []
let is_empty = function [] -> true | _ -> false
let push = List.cons
let peek = function [] -> raise Empty | x :: _ -> x
let pop = function [] -> raise Empty | _ :: s -> s
```

**编译方法一**：

```ocaml
ocamlc -c mystack.mli
ocamlc -o mystack mystack.ml
```

编译方法二（dune）。

### Incomplete Compilation Units

1、缺少 interface 文件。这没问题，实际上从开始学习至此，基本上都是以这种模式开发的。

2、缺少 implementation 文件。如果有一个 stack interface，它有两种实现，那么按 Java 习惯，可能会这么分隔文件：

```ocaml
(********************************)
(* stack.mli *)
type 'a t
val empty : 'a t
val push : 'a -> 'a t -> 'a t
(* etc. *)

(********************************)
(* listStack.ml *)
type 'a t = 'a list
let empty = []
let push = List.cons
(* etc. *)

(********************************)
(* customStack.ml *)
(* omitted *)
```

但这种方式不可行，因而作为一个 unit，名称必须完全一致。可以尝试的方法是：

```ocaml
(********************************)
(* stack.ml *)
module type S = sig
  type 'a t
  val empty : 'a t
  val push : 'a -> 'a t -> 'a t
  (* etc. *)
end

(********************************)
(* listStack.ml *)
module M : Stack.S = struct
  type 'a t = 'a list
  let empty = []
  let push = List.cons
  (* etc. *)
end

(********************************)
(* customStack.ml *)
module M : Stack.S = struct
  (* omitted *)
end
```

这里是显式定义了 module type 的 signature，然后在别的文件里引用之。需要注意的是：

* module type 定义在 .ml 文件中，而非 .mli，因为这里不是在创建编译单元。
* 使用短名称，否则会出现诸如 `Stack.Stack` 这样的引用。

另一种分隔代码的方法是：

```ocaml
(********************************)
(* stack.mli *)
module type S = sig
  type 'a t
  val empty : 'a t
  val push : 'a -> 'a t -> 'a t
  (* etc. *)
end

module ListStack : S

module CustomStack : S

(********************************)
(* stack.ml *)
module type S = sig
  type 'a t
  val empty : 'a t
  val push : 'a -> 'a t -> 'a t
  (* etc. *)
end

module ListStack : S = struct
  type 'a t = 'a list
  let empty = []
  let push = List.cons
  (* etc. *)
end

module CustomStack : S = struct
  (* omitted *)
end
```

这种方法的问题是，不得不 duplicate `Stack.S`。在 OCaml 中无法自动 import 一个已经声明的 module type。

## 5.6 Functional Data Structures

函数式数据结构具有两个重要特点：

* 不可变性：不依赖于 mutability
* 持久性：persistent

更新数据结构时，不修改原有版本，而是产生一个新版本。好的语言实现会确保未修改部分在新旧版本之间得以共享，以减少开销。

值得注意的是，函数式数据结构可以通过函数式或命令式编程语言来实现。

### Lists

list 是函数式的，它不可修改，也是持久的。

### Stacks

```ocaml
module type Stack = sig
  type 'a t
  exception Empty
  val empty : 'a t
  val is_empty : 'a t -> bool
  val push : 'a -> 'a t -> 'a t
  val peek : 'a t -> 'a
  val pop : 'a t -> 'a t
  val size : 'a t -> int
  val to_list : 'a t -> 'a list
end

module ListStack : Stack = struct
  type 'a t = 'a list
  exception Empty
  let empty = []
  let is_empty = function [] -> true | _ -> false
  let push = List.cons
  let peek = function [] -> raise Empty | x :: _ -> x
  let pop = function [] -> raise Empty | _ :: s -> s
  let size = List.length
  let to_list = Fun.id
end
```

在这个实现中，Stack 是不可变的，同时也是 persistent 的：push、pop 的前面暗示，它们都返回一个新的 Stack。

使用时可注意 pipeline：

```ocaml
open ListStack;;
let s = empty |> push 1 |> push 2;;
let s' = pop s;;
to_list s;;
to_list s';;
```

### Options vs. Exceptions

```ocaml
module type Stack = sig
  type 'a t
  val empty : 'a t
  val is_empty : 'a t -> bool
  val push : 'a -> 'a t -> 'a t
  val peek : 'a t -> 'a option
  val pop : 'a t -> 'a t option
  val size : 'a t -> int
  val to_list : 'a t -> 'a list
end

module ListStack : Stack = struct
  type 'a t = 'a list
  exception Empty
  let empty = []
  let is_empty = function [] -> true | _ -> false
  let push = List.cons
  let peek = function [] -> None | x :: _ -> Some x
  let pop = function [] -> None | _ :: s -> Some s
  let size = List.length
  let to_list = Fun.id
end
```

注意其中 peek、pop 签名的变化。

两种方式需要 tradeoff。使用 option，程序确实会少一些异常，因而更 robust，但 pipeline 操作也不支持了；使用异常，所需代码更少，但更不 robust。

目前 OCaml 标准库中开始提供两个版本的接口，如：

```ocaml
val peek : 'a t -> 'a
val peek_opt : 'a t -> 'a option
val pop : 'a t -> 'a t
val pop_opt : 'a t -> 'a t option
```

### Maps

```ocaml
module type Map = sig
  (** [('k, 'v) t] is the type of maps that bind keys of type ['k] to
      values of type ['v]. *)
  type ('k, 'v) t

  (** [empty] does not bind any keys. *)
  val empty  : ('k, 'v) t

  (** [insert k v m] is the map that binds [k] to [v], and also contains
      all the bindings of [m].  If [k] was already bound in [m], that old
      binding is superseded by the binding to [v] in the returned map. *)
  val insert : 'k -> 'v -> ('k, 'v) t -> ('k, 'v) t

  (** [lookup k m] is the value bound to [k] in [m]. Raises: [Not_found] if [k]
      is not bound in [m]. *)
  val lookup : 'k -> ('k, 'v) t -> 'v

  (** [bindings m] is an association list containing the same bindings as [m].
      The keys in the list are guaranteed to be unique. *)
  val bindings : ('k, 'v) t -> ('k * 'v) list
end

module AssocListMap : Map = struct
  (** The list [(k1, v1); ...; (kn, vn)] binds key [ki] to value [vi].
      If a key appears more than once in the list, it is bound to the
      the left-most occurrence in the list. *)
  type ('k, 'v) t = ('k * 'v) list
  let empty = []
  let insert k v m = (k, v) :: m
  let lookup k m = List.assoc k m
  let keys m = List.(m |> map fst |> sort_uniq Stdlib.compare)
  let bindings m = m |> keys |> List.map (fun k -> (k, lookup k m))
end
```

`List.assoc` 就是为 association list 量身定做的。

## 5.7 Module Type Constraints

前面一直在“赞美”封装的好处，现在来看一个反直觉的做法——选择性违反封装原则（selectively violate encapsulation）。

以下是一个例子，它表示一个环：

```ocaml
module type Ring = sig
  type t
  val zero : t
  val one : t
  val ( + ) : t -> t -> t
  val ( * ) : t -> t -> t
  val ( ~- ) : t -> t  (* additive inverse *)
  val to_string : t -> string
end
```

下面是一种 环 的实现：

```ocaml
module IntRing : Ring = struct
  type t = int
  let zero = 0
  let one = 1
  let ( + ) = Stdlib.( + )
  let ( * ) = Stdlib.( * )
  let ( ~- ) = Stdlib.( ~- )
  let to_string = string_of_int
end
```

由于抽象类型，IntRing 的值类型显示为 `IntRing.t`，从而调试时不太方便，甚至还要定义 to_string、printer。而如果有另一个 FloatRing，则需要再完成同一套操作。

```bash
IntRing.(one + one)
- : IntRing.t = <abstr>

IntRing.(one + one |> to_string)
- : string = "2"

let pp_intring fmt i =
  Format.fprintf fmt "%s" (IntRing.to_string i);;
#install_printer pp_intring;;

IntRing.(one + one)
- : IntRing.t = 2
```

之前有过一种处理：先定义 module，在为其添加 module type 约束：

```ocaml
module IntRing = struct
  type t = int
  let zero = 0
  let one = 1
  let ( + ) = Stdlib.( + )
  let ( * ) = Stdlib.( * )
  let ( ~- ) = Stdlib.( ~- )
  let to_string = string_of_int
end

module _ : Ring = IntRing
```

此时，对于 IntRing 来说，t 就是 int，而不是抽象的。要达到这一点，还有另一种方法：

```ocaml
module type INT_RING = Ring with type t = int

module IntRing : INT_RING = struct
  type t = int
  let zero = 0
  let one = 1
  let ( + ) = Stdlib.( + )
  let ( * ) = Stdlib.( * )
  let ( ~- ) = Stdlib.( ~- )
  let to_string = string_of_int
end
```

对于 INT_RING，它的 t 就等同于 int，于是用起来也会很方便：

```ocaml
IntRing.(one + one)
IntRing.(1 + one)

- : int = 2
```

对于 FloatRing，也可以类似来定义，不过你应该可以想到，这样一种”模式“，OCaml 应该有更好地支持：

```ocaml
module FloatRing : Ring with type t = float = struct
  type t = float
  let zero = 0.
  let one = 1.
  let ( + ) = Stdlib.( +. )
  let ( * ) = Stdlib.( *. )
  let ( ~- ) = Stdlib.( ~-. )
  let to_string = string_of_float
end

(* tests *)
# FloatRing.(1.0+one);;
- : float = 2.
```

### Constraints（约束）

有两种类型的 Constraints：

* `T with type x = t`：这种前面已经看到，T 是 module type，t 是类型，x 是类型名
* `T with module M = N`：表示两个模块中所有类型的 equality。

多个约束可以使用 and 连接。

`T with type x = t` 与 T 的唯一不同，是其中的 type x 被替换为 `type x = t`，如下面的例子：

```ocaml
module type T = sig type t end
module type U = T with type t = int

module type T = sig type t end
module type U = sig type t = int end
```

PS：这里的 `M = N`，是指两者的 types 被限定为必须相等，但并不意味着两者的类型完全一样。

## 5.8 Includes

看一个最简单的例子：

```ocaml
module A = struct
  let x = 1
  let f y = y + 1
end

module B = struct
  include A
  let z = f x
  let x = 2
end
```

这样，B 就有了 A 中定义的x、f。

这有点像是 OOP 中继承的实现。还有一个值得注意的点是，A 的 x 在 B 中被重写了。

总的来说，`include` 作用是，在一个模块中包含另一个模块的所有成员，或一个模块类型（module type）包含入另一个模块的所有成员。例子：

```ocaml
module ListSetExtended = struct
  include ListSet
  let of_list lst = List.fold_right add lst empty
end
```

这里的效果是：ListSetExtended 模块包含了 ListSet的所有定义，同时也添加了新的定义，理论上也完全可以重写某些定义。于是，这里和 inheritance 就比较相像了。

上段代码近似于如此编码：

```ocaml
module ListSetExtended = struct
  (* BEGIN all the includes *)
  type 'a t = 'a ListSet.t
  let empty = ListSet.empty
  let mem = ListSet.mem
  let add = ListSet.add
  let elements = ListSet.elements
  (* END all the includes *)
  let of_list lst = List.fold_right add lst empty
end
```

即将另一个模块的定义一个个 ”copy“过来，但并非真的 copy。比如 ListSet 修改后，ListSetExtended 也会得到更新，这正是我们期望的。

module type 的 Include 也是类似的：

```ocaml
module type SetExtended = sig
  include Set
  val of_list : 'a list -> 'a t
end
```

### Include vs. Open

```ocaml
module M = struct
  let x = 0
end

module N = struct
  include M
  let y = x + 1
end

module O = struct
  open M
  let y = x + 1
end
```

看什么三个 module 的签名信息：

```ocaml
module M : sig val x : int end
module N : sig val x : int val y : int end
module O : sig val y : int end
```

可以看到，include 会 import 定义作为自己的定义，因而 export 到外部；而 open 仅在局部可见。

## 5.9 Functors

在范畴论中，Functor 意为函子，颇为抽象，不过在 OCaml 中，Functor 的概念很简单——从模块到模块的**函数**。

看一个小例子：

```ocaml
module type X = sig
  val x : int
end

module IncX (M : X) = struct
  let x = M.x + 1
end

module type X = sig val x : int end
module IncX : functor (M : X) -> sig val x : int end
```

IncX 是一个 functor，其输入是 M，类型为 X，而输出是 `struct ... end`，因而是另一个 module。

另一个角度是，将 IncX 视为一个**参数化的结构**（parameterized structure）。

functor 既为函数，便可以 apply 之。

```ocaml
module A = struct let x = 0 end
module B = IncX (A)

let _ = assert (B.x = 1)

module C = IncX (B)
let _ = assert (C.x = 2)
```

`module B = IncX (A)` 反映出了 functor 的本质：**从模块到模块的函数**。

注意：调用时，甚至可以使用匿名模块：

```ocaml
module C = IncX (struct let x = 41 end)
let _ = assert (C.x = 42)
```

### 语法与语义

在 functor 定义中：

```ocaml
module F (M : S) = ...
end
```

type annotation 中的 S 与括号是必需的。唯有如此，OCaml 才能进行类型推断。

functor 与函数一样，也可以匿名：

```ocaml
module F (M : S) = ...
module F = functor (M : S) -> ...
```

上面第二行是匿名 functor，其作用类似于 函数的 fun。

上面的 M 类似于参数名，functor 可包含多个参数：

```ocaml
module F (M1 : S1) ... (Mn : Sn) = ...
```

TODO：

Functor Type；Map Module。

## 5.10 Summary

OCaml 的**模块系统**与更主流语言的机制不尽相同（比如 Java、Python 等），不过，在了解这些不同机制之后，遇到新语言的模块系统理解起来也不会太难。

**抽象类型**和**签名**，提供了抽象机制，与 Java 的 visibility modifier、接口相近。

「**类型可以是抽象的**」，这一思想本身对于编程语言设计极为重要，而这一点在 OCaml 中颇为醒目的，其他语言通常并非明显。

Functor 是 OCaml 的高级特性，乍看之下很神秘。但请记住，它只是从模块到模块的函数而已。

Functor 和 include 提供了代码复用功能。Java 中 class 的扩展，可视为 functor 和 include 的一种组合。扩展一个类，像是写一个 functor，将 base class 作为输入，include 之，然后添加一些函数。但理论上，functor 比 class extension 更通用。

学习 OCaml 的模块系统，也许最重要的一点，是理解那些超越了特定语言的模块化概念：命名空间、抽象和代码复用。**不同语言以不同的机制来实现类似这些相通的目标**。在认识了几种很不同的语言后，你就能在学习下一种语言时，更清晰地识别其理念。
