---
title: nlp-in-action-ch01
date: 2019-08-18 02:46:54
tags:
    - NLP

categories: [NLP]
---

# NLP实战

## Chatbot Recurrent Pipeline

1. Parse: tokenizer, NER, extract info., reduce dim
2. Analyze: spelling, grammar, sentiment, humanness, CNN, GAN
3. Generate: search, templates, FSM, RNN...
4. Execute: generate & classify...

## 序

2013年之后，NLP和聊天机器人的应用变得日益广泛，如愈来愈智能的Google Search、智能手机的自动完成功能。Gmail的`Smart Reply`令作者印象深刻。

从经典的马尔科夫链（词频、ngrams、条件概率等），可以实现一些颇为有用的功能，如Peter Norvig的[spelling corrector](https://norvig.com/spell-correct.html)。

PS：Peter的文章2016年有更新，或许会有新的启发，文章主要涉及传统的基于统计的语言模型。

接下来是**隐语义分析（Latent Semantic Analysis, LSA）**，大致是持续跟踪那些共现的词，从而可将这些词归为**topic**。LSA将句子或文档的意思压缩为一个向量。

**gensim**库实现了`Word2vec`词向量，可将

# 第一部分 善谈的机器

1-4章

# Ch01 NLP概述



# Ch02 构建词汇表（Tokenizer）

# Ch03 关于词的数学（TF-IDF）

# Ch04 词频蕴含的意义（Semantic Analysis）

# 第二部分 深度学习

5-10章

# Ch05 初识神经网络（感知机与反向传播）

# Ch06 以词向量推理（Word2Vec）

# Ch07 卷积神经网络（CNN）

# Ch08 循环神经网络（RNN）

# Ch09 LSTM

# Ch10 Seq-to-seq模型

# 第三部分 来到现实世界

11-13章

# Ch11 信息提取（NER与问题回答）

# Ch12 对话引擎

# Ch13 Scaling up




