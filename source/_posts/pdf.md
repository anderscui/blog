---
title: Under the hood of PDF
date: 2021-04-30 12:03:29
tags:
---

refs:
* iText in Action

# Overview 

Different parts of PDF file:
* header
* body
* cross-reference table
* trailer

Also, the Carousel Object System.

# PDFs inside-out

from a de facto to an ISO standard.

## why and how?

1985年，Adobe 引入了 PostScript Page Description Language。同年，Adobe 也开发了 Adobe Illustrator。这两款产品引发了桌面 publishing 的革命。但 Adobe 的创建者们感觉还缺少了点什么。

<!-- more -->

1991年，John Warnock 写了 Carmelot Paper，在其中写道：

一个特别的问题是，大多数程序可以打印至不同的打印机，但没有一种通用的方式来通信和查看这些打印信息。行业所亟需的是在不同的机器配置、OS、网络之间的通用通信机制。

作为这个文章的结果，一个新项目启动了，`Carousel` 是初始代号，之后修改为 `Acrobat`。新文档格式原称为 IPS，很快就改为 PDF（Potable Document Format）。

## The history of PDF

尽管 Carousel 只是最初代号，但现在仍用于表示**PDF文件的组成方式**。在最低层次，iText 使用继承自 `PdfObject` 的对象，在 Carousel 中，有一系列基础的 PDF 对象。

### Basic PDF Objects

共有 8 种（ISO-32000-1）。

* Boolean
* Numeric
* String
* Name
* Array
* Dictionary
* Stream: like a string object, a stream is a sequence of bytes, mainly used for large amounts of data
* Null

示例：

* PdfAction, PdfFormField, PdfOutline: 是 PdfDictionary 的子类
* PdfRectangle 是 PdfArray 的特殊类型
* PdfDate 是 PdfString 的子类

这些类型称为 `direct`。

## The PDF file structure

通过文本编辑器打开一个 pdf 文件，可以看到 pdf 文件包含以下部分：

* header: version、comment
* body：seq of indirect objects: page、outline、annotation
* cross-reference: 
* trailer: loc of the cross ref table and ...

