---
title: spacy
date: 2019-08-15 22:52:38
tags:
  - NLP, Spacy

categories:
  - NLP
---

# 架构

spaCy的核心数据结构是`Doc`和`Vocab`。`Doc`对象持有**token序列**及其标注（annotation）信息，`Vocab`对象则持有一个**查找表（look-up table）**，从而可在文档间共享信息。集中管理字符串、词向量与词法属性，避免了反复copy数据。

文本标注也是类似，`Doc`持有数据，`Span`和`Token`只是它的视图。`Doc`由`Tokenizer`构建，之后通过pipeline原地修改（modified in place）。`Language`对象协调以上诸组件，它接受原始文本，将其送入pipeline，返回一个标注了的文档，同时也参与了模型训练和序列化过程。

## 容器对象

* Doc：包含语言标注的容器
* Span：表示Doc的一个切片
* Token：单个token，如词、标点、空格等
* Lexeme：词位，词汇表的一个entry，不带任何上下文信息，因此也没有POS。

## Pipeline

* Language
* Tokenizer
* Lemmatizer
* Tagger
* Matcher
* PhraseMatcher
* EntityRuler
* ...

## 其它类

* Vocab
* StringStore
* Vectors
* ...



