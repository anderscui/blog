---
title: interesting-python-libraries
date: 2020-08-11 18:45:24
tags:
    - Python

categories:
    - Python
---

偶遇的有趣的 Python 库。

# [attrs](https://www.attrs.org/en/stable/index.html)

`attrs` 将为你找回编写类（classes）的乐趣，能够减轻大量的 [dunder methods](https://nedbatchelder.com/blog/200605/dunder.html)。我们写出的类型的代码会更简洁，更少出错。

## 日常应用

* 类型标注：编写自文档代码，如借助于 `attr.ib`。
* 类型初始化
* Hashing
* ...

声明 attributes 后，可以获得：

* a concise and explicit overview of class's attribs
* a nice human-readable `__repr__`
* comparison methods (equality and ordering)
* an initializer
* ...

自 Python 3.6 开始，还可以不使用 `attr.ib` 而是直接用 [type annotations](https://www.attrs.org/en/stable/types.html)，当然，前提是你希望将标注所有的属性。

借助于 attrs，可在代码中方便地使用自定义类型而非 tuples，以及令人困惑的 namedtuples。之所以使用 tuples，往往是为了方便省事儿，但有了 attrs，写个自定义类型也不麻烦了。

## attrs 不是什么

attrs 不会偷偷地使用 meta classes、runtime introspection 以及 interdependencies。它所做的是：

* 使用你的声明
* 为你定义 dunder methods
* 将这些方法 attach 到你的类型

因此不会有任何的性能损失。

## attr.s 与 attr.ib

它们看起来很不自然？把 `.` 去掉试试。

## 既然已经有了...，为何还要用 `attrs`？

### tuples

* 可读性：无须赘述
* 可扩展性：增加元素，会导致一系列代码失效

### namedtuples

> If you want a tuple with names, by all means: go for a namedtuple. 5 But if you want a class with methods, you’re doing yourself a disservice by relying on a pile of hacks that requires you to employ even more hacks as your requirements expand.

### Data Class

`attrs` 与 `Data Class` 很像，但 attrs 有功能更多，演进更快速。Data Class 只能用于 py37 之后。

### dicts

如果把值看作kv 对，tuples 可看作是隐藏了 keys；dict 看起来更完备一些，但是此时的 keys 往往是 str。

> if your dict has a fixed and known set of keys, it is an object, not a hash. So if you never iterate over the keys of a dict, you should use a proper class.

# Data Class

在了解了 attrs 后，感觉**Data Class**可能更可取，因为它属于标准库，更可靠，而且一般不需要考虑py37 之前的版本。

```
from dataclasses import dataclass

@dataclass
class DataClassCard:
    rank: str
    suit: str

# init: positional, keywords
queen_of_hearts = DataClassCard('Q', suit='Hearts')
# repr
print(queen_of_hearts)
# eq
print(queen_of_hearts == DataClassCard('Q', 'Hearts'))
```

通过这个简单的例子可以看到 dataclass 比常规自定义类型多做的三件事情，同时也优于用作数据容器的 tuple、dict。

# pyqtgraph

# pywebview

# colorama

# begins

# arrow

# parsedatetime

# Cython

# https://github.com/vinta/awesome-python

