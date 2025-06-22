---
title: exploratory-data-analysis-nlp-tools
date: 2020-07-30 10:42:26
tags:
  - NLP
  - Data Analysis

categories:
  - [NLP]
  - [Data Analysis]
---

[原文链接](https://neptune.ai/blog/exploratory-data-analysis-natural-language-processing-tools)

Exploratory Data Analysis（探索性数据分析，EDA）是分析数据集以获取其主要特征的一种方法，通常会结合使用可视化方法。它主要是来查看，在借助于正式的模型与假设检验之外，数据还能告诉我们什么。在历史上，John Tukey是推广EDA的关键人物（1977年，出版同名书籍）。

本文通过实例讨论在NLP中的EDA。

# 开始之前

## 安装

```
pip install \
   pandas matplotlib numpy \
   nltk seaborn sklearn gensim pyldavis \
   wordcloud textblob spacy textstat
```

## 数据

[ABC News Headlines](https://www.kaggle.com/therohk/million-headlines)

# 文本统计

文本的可视化统计数据很简单，但能给出有效的信息。

* 词频
* 句长
* 平均词长
* ...

以此了解文本数据的基本特征。对continuous data，使用histogram，对categorical data，使用bar chart。



参考：
[Exploratory_data_analysis](https://en.wikipedia.org/wiki/Exploratory_data_analysis)


