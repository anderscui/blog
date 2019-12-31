---
title: Spark SQL
date: 2019-05-31 14:04:42
tags:
  - Apache Spark

categories:
  - Apache Spark
---

RDD是无结构的，Spark 1.6引入了Structured API，后者是多数场景下更为适用。

Spark SQL基于Spark Core，包含两部分：

* DataFrame & Dataset
* Catalyst optimizer

结构化数据结构具有`schema`定义。

# Creating DataFrames




