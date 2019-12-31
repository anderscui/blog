---
title: Python中的可迭代类型
date: 2019-05-10 22:26:38
tags:
  - Python

categories:
  - Programming
---

本文主要整理自《Fluent Python》的第14章。

**迭代（iteration）**在数据处理中不可或缺的。当数据在内存中放不下时，我们需要偷一下懒（lazily），每次按需取一个数据项，这就是所谓的迭代器（iterator）模式。

Python 2.2（2001）添加了`yield`关键字，用以构造**生成器（generator）**，生成器可达成迭代器的效果。而且，在Python社区中，生成器与迭代器同义。

Python中的每个集合（collection）都是**可迭代的（iterable）**。迭代器在内部用于：

* for 循环
* 集合类型的构造与扩展
* 列表等类型的推导
* tuple unpacking
* *args的unpacking

本章涵盖以下主题：

* `iter`函数
* 如何实现经典的迭代器模式
* 生成器工作机制
* 经典的迭代器可用生成器替代
* `yield from`语句
* 生成器与**协程（coroutine）**看起来相似，本质上却非常不同

# 序列类型1：词序列

```python
import re
import reprlib

RE_WORD = re.compile('r\w+')


class Sentence:
    def __init__(self, sentence):
        self.sentence = sentence
        self.words = RE_WORD.findall(sentence)

    def __getitem__(self, item):
        return self.words[item]

    def __len__(self):
        return len(self.words)

    def __repr__(self):
        return f'Sentence({reprlib.repr(self.sentence)})'
        

if __name__ == '__main__':
    sent = Sentence('"The time has come," the Walrus said,')
    print(sent)

    for word in sent:
        print(word)

    print(list(sent))
    print(sent[0])
```

## 为何序列是可迭代的？

当解释器对一个对象`x`进行迭代时，它会自动调用`iter(x)`，该内置函数会：

* 检查对象是否实现了`__iter__`，有则调用之，并获取到一个迭代器；
* 否则检查`__getitem__`，有则调用之，并创建一个迭代器；
* 否则抛出`TypeError`

因为**序列**都实现了`__getitem__`，因此它们同时也是可迭代的，而标准库中的序列类型也会同时实现`__iter__`，我们实现序列类型时也需要如此。

这一处理方式导致了一个有趣的事实：一个iterable的对象不一定满足`isinstance(o, abc.Iterable)`。判断对象是否可迭代的准确方式是，对其迭代并捕获异常。

# 可迭代类型与迭代器类型

可迭代与迭代器的区别是，Python从iterable获取iterator。

下面的例子演示iterator类型的用法，不使用for循环：

```python
s = 'abc'
it = iter(s)
while True:
    try:
        print(next(it))
    except StopIteration:
        del it
        break
```

`StopIteration`表明迭代器已经迭代结束，在使用for循环时，该异常在其内部被处理掉了。

Iterator接口有两个方法：`__next__`、`__iter__`。

# 序列类型2：生成器函数

```python
class Sentence:
    def __init__(self, sentence):
        self.sentence = sentence
        self.words = RE_WORD.findall(sentence)

    def __iter__(self):
        for word in self.words:
            yield word

    def __len__(self):
        return len(self.words)

    def __repr__(self):
        return f'Sentence({reprlib.repr(self.sentence)})'
```

这个版本的实现里，使用`__iter__`代替了`__getitem__`，因此更“地道”的可迭代实现。

## 分析

任何含有`yield`关键字的函数都是**生成器函数（generator function）**，生成器函数的返回值是生成器对象。

# 序列类型3：更懒一点

在前两个版本的实现中，`__init__`会立即计算出所有的words，不管后面会不会用到，为了性能与占用内存计，我们希望类型能“更懒一点”。

使用Python 3时，每当你考虑“有否更懒的方式”，答案一般是肯定的。这里可以使用`re.finditer()`：

```python
class Sentence:
    def __init__(self, sentence):
        self.sentence = sentence

    def __iter__(self):
        for match in RE_WORD.finditer(self.sentence):
            yield match.group()

    def __repr__(self):
        return f'Sentence({reprlib.repr(self.sentence)})'
```

修改之后，原来的`self.words`不再需要。不过，这还不是最短的实现。

# 序列类型4：生成器表达式

生成器表达式可以理解为惰性版的列表推导：如果列表推导是一个列表工厂，那么生成器表达式就是一个生成器工厂。

```python
class Sentence:
    def __init__(self, sentence):
        self.sentence = sentence

    def __iter__(self):
        return (match.group() for match in RE_WORD.finditer(self.sentence))

    def __repr__(self):
        return f'Sentence({reprlib.repr(self.sentence)})'
```

生成器表达式只是语法糖，它们总是可以被替换为生成器函数，只是有时更为方便。

# 定义自己的range生成器

```python
class ArithmeticProgression:

    def __init__(self, begin=0, step=1, end=None):
        self.begin = begin
        self.step = step
        self.end = end

    def __iter__(self):
        result = type(self.begin+self.step)(self.begin)
        forever = self.end is None
        index = 0
        while forever or result < self.end:
            yield result

            index += 1
            result = self.begin + self.step * index
```

这里的`__iter__`函数只是返回一个生成器，所以这个类实际上可以简化为生成器函数。

# 标准库中的生成器函数

这类函数定义在`itertools`和`functools`中，分为以下几类：

## Filtering

* compress：通过一个iterable对另一个过滤；
* dropwhile
* filter
* filterfalse
* islice：对任一iterable实施slice操作
* takewhile：

## Mapping

* accumulate
* enumerate
* map
* starmap

## Merging Generators

* chain
* chain.from_iterable
* product
* zip
* zip_longest

## Expanding Generators

* combinations
* combinations_with_replacement
* count
* cycle
* permutations
* repeat

## Rearranging Generators

* groupby
* reversed
* tee

# yield from

PS：py3.3引入的新语法。

# 生成器作为协程

py2.5引入了协程，协程向生成器对象添加了新的方法——主要是`.send()`。这一“enhancement”实际上改变了生成器的本质：如此一来，它们变成了协程。David Beazley尝言：

* 生成器产生迭代器（用于迭代）
* 协程是数据的消费者
* 不要将两个概念混在一起使用
* 协程与“迭代”无关


