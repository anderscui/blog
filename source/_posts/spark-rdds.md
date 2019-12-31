---
title: RDDs and in Spark
date: 2019-05-31 11:26:25
tags:
  - Apache Spark

categories:
  - Apache Spark
---

# Rich Set of Operations

RDD提供了丰富的数据处理操作API，如transformation、filtering、grouping、joining、aggregation、sorting和counting。

PS：这些操作均是粗粒度的（coarse-grained），即同一操作被应用于多行，而非特定的一行。

RDD表示对数据的抽象，包含五部分信息：

* partition集合，亦为整个数据集的chunk；
* 对父级RDD的依赖关系
* 对所有row进行计算的函数
* partition scheme的元数据
* 集群中数据的位置信息

Spark使用前三种信息以实现：确定RDD执行的顺序；失败恢复。

# RDD Operations

RDD操作是粗粒度的，每一row表示为一个Java对象。Spark不关心row的结构，RDD的使用者负责如何处理每一行。更高层次的抽象如Spark SQL则在此基础上提供了其它计算。

RDD操作可分为两类：

* Transformation（lazy）：一个新的RDD
* Action（eager）：返回结果或将结果写入磁盘

# Creating RDDs

```scala
val strs = Array("Spark", "RDDs")
val strRDD = spark.sparkContext.parallelize(strs)

val fileRDD = spark.sparkContext.textFile("/dev/spark-2.4.3-bin-hadoop2.7/README.md")
```

# Transformation



