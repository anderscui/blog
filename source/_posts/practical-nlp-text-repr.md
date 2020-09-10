---
title: practical-nlp-text-repr
date: 2020-07-13 14:55:53
tags:
  - NLP

categories:
  - NLP
---

# Text Representation（文本表示）

## Distributed Representations（分布式表示）

前述几种基本表示法有着明显的缺陷，比如：

* 它们都是离散化表示，即把语言单位（词、ngrams等）视为原子单位，因此不能捕捉到词之间的关系
* 特征向量是稀疏和高维的表示，维度与词汇表大小一致，从而导致向量的绝大部分元素为0，这一结果不利于学习，也使得计算过程颇为低效
* 不能有效处理未登录词

因此需要能够学习到低维向量表示的新方法，这些方法在过去六七年（2013年开始）中得到了迅速发展。它们使用NN架构创建出稠密（而非稀疏）的、低维（而非高维）的词与文本的表示。了解这些方法之前，需要先了解分布语义学。

## Distributional Semantics（分布语义学）

分布语义学是基于语言项（linguistic items）在大量语言数据上的分布性质，对其相关性进行量化与分类的一系列理论与方法。它的基本思想基于所谓的分布假设：具有相似分布的语言项也具有相似的语义。

### Distributional Hypothesis

分布假设背后的思想是：”a word is characterized by the company it keeps“（Firth 1950s）。分布假设是统计语义学的基础。

孩子在学习语言时，对于未见过的词，仍然可以正确使用，也是基于此假设（通过已了解词的用法去泛化）。

### Distributional Similarity（分布相似度）

它的要义是：词的语义可通过其上下文来理解。

可以理解为：两个词在分布上越相似，其语义即越相似。

### Distributional Representation（分布表示）

获取一个词在”分布“意义上的向量表示。



