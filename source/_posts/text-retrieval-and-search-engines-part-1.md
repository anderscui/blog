---
title: Text Retrieval and Search Engines(1)
date: 2017-07-04 00:34:11
tags:
  - Search Engine
  - NLP

categories:
  - Search Engine
  - NLP
---

Coursera课程[文本检索与搜索引擎（Text Retrieval and Search Engines）](https://www.coursera.org/learn/text-retrieval/home/welcome)的学习笔记。

# 课程结构

![Course Schedule](/images/text-retrieval-course-schedule.png)

# 1、TODO

第一部分将涵盖上图中1-5部分的内容。

## 1.1 Natural Language Content Analysis

这无疑是处理任何文本数据的第一步，本节包含三个小主题：

* 什么是NLP？
* NLP领域研究的现状
* NLP与文本检索

### 1.1.1 什么是NLP？

来看一个NLP的简单例子：

![An example of NLP](/images/an-example-of-nlp.png)

如果想让计算机理解这个句子，需要哪些步骤呢？类似于人类的理解过程，首先需要知道它包含哪些词，以及各个词的词性。这个过程称为**词法分析**（Lexical analysis）或**词性标注**（Part-of-speech tagging）。

接下来需要了解句子的语法结构，即这些词如何构成更复杂的语法结构。如A和dog构成名词短语，on、the及playground构成介词短语等等。这个过程称为**语法分析**（Syntactic analysis）。

了解了句子的词法结构和语法结构，计算机仍不足以了解句子的意义。这时需要的是**语义分析**（Semantic analysis）。对计算机来说，需要将词和短语这样的成分对应到某些symbol，同时还要有symbol之间的关系，比如上图中的Chasing就是这样一种关系。更进一步，在关系之上，我们还可以进行**推理**（Inference），比如我们有一条规则：如果某实体被狗追，那么该实体会害怕。在此规则下，我们可以推理出一个结论：the boy is scared。

另外还可以考虑，一个人为何说出这样一句话？ta的意图是什么？一种可能是，ta在提醒另一个人把狗牵回来。这个过程称为**Pragmatic analysis**，即分析“语言行为“本身。

计算机要理解一个如此简单的句子都需要很繁琐的过程。人类理解起来要容易得多，这是因为人的大脑早已有了庞大的”知识“库，而计算机则需要从头学起。

直觉上，人类学习语言并非如洛克的”白板论”那样，一个人对于语言的理解应当有相当的部分来自于遗传。总之，对于可怜的计算机而言，NLP是很难的。

### 1.1.2 NLP是很难的

”自然语言“当然是为人类的有效沟通而设计，其结果是：我们会大量的”**常识**“内容，并假设听者或读者是能够理解的；语言中存在大量的**歧义**（ambiguity），我们假设听者或读者能够仔细理解之。

当一个人缺乏**常识**，与ta沟通起来会感觉困难。对NLP来说，以Siri之类的应用为例，我们的感觉不是与它很难沟通，而是完全无法沟通。当一个人说的话有**歧义**，人类也会觉得理解起来有困难，拿不准其准确含义，遑论计算机了。所以，常识的缺乏和歧义使得NLP格外困难。

歧义的常见情况有：

* 词的歧义：不同词性；多义词；
* 语法结构的歧义：修饰语与被修饰语的不同结合；介词短语附着（PP Attachment），”A man saw a boy _with a telescope_；
* 首语重复（Anaphora）解析：John persuaded Bill to buy a TV for _himself_；
* 预先假定（Presupposition）："He has quit smoking"暗示他曾吸过烟。

### 1.1.3 NLP研究现状

![State of the Art NLP](/images/state-of-the-art-nlp.png)

词性标注准确率较高；语法解析层面，部分解析（Partial Parsing，即句子的一部分，如短语级别）达到90%以上。

语义解析的进展则相当不好。不过在某些特定应用上取得了一些进展，如实体识别、关系提取、情感分析。

推理和语言行为方面的表现则是更差。

值得一提的是，尽管说词性标注和部分解析的准确率已经达到较高的程度，但这些评测都是基于特定的测试数据集，此类测试集常常是新闻类数据，这会导致一定的偏差。因此相应的算法应用到不同领域中可能未必有同样好的效果。

### 1.1.4 NLP与文本检索

文本检索通常涉及大量而广泛的文本，如果希望其中的NLP技术是健壮和高效的，那么目前来看只能采用浅层的NLP。文本的**词袋**（Bag of words）表示是最简单的一种，它无疑丢弃了文本的大量信息，但对于大部分（不是全部）搜索任务而言却是够用的。

某些文本检索技术可以自然地解决NLP问题，如语义消歧。

但是，对于复杂的搜索任务，更深层的NLP技术仍是必需的，比如**知识图谱**（Knowledge Graph）。











