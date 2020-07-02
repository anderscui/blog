---
title: Deep Learning in PyTorch
date: 2020-06-12 17:11:02
tags: Deep Learning, PyTorch
---

# Deep Learning in PyTorch

该书的读书笔记，想从此系统的学习DL，尤其是NLP相关。

# 1. Introducing DL and PyTorch library

### The deep learning revolution

即使到2000年代末，ML领域仍然很依赖于**特征工程（feature engineering）**。特征用于将输入数据转换为数值，以用于下游算法。特征工程的目标是接受原始数据，得到其**表示（representation）**。

DL则将特征寻找过程自动化了。虽然，DL中仍需要特征工程，它的重点不再是精心构建特征，而是通过对数学实体的操作”发现“这些特征。通常情况下，这些自动发现的表示优于手工构建的。

### 1.3.2 Immediate versus deferred execution

DL库们的一个关键区别是立即执行与延迟执行。PyTorch的易用性很大程度上来自于它的立即执行的实现方式。

PyTorch默认情况下使用立即执行（eager mode）。

NN的基础组块是neuron，大量neuron连接在一起构成网络。之前TF默认使用静态图，而PyTorch使用动态图，之后两者逐渐同时支持两种模式。

## 1.4 PyTorch 的组件

其核心是多维数组——tensor，以及对tensor的诸多操作，这些都在`torch` 模块中。

创建NN的模块在 `torch.nn` 中，如全连接层，卷积层，激活函数，损失函数之类。

要训练模型，我们还需要：数据源管理，optimizer。加载与处理数据的工具在 `torch.util.data`，两个主要的类是 `Dataset` 和 `DataLoader`。optimizer位于 `torch.optim` 模块中。

# 2. It starts with a tensor

在很高的层次上，DL可以理解为将一种输入转换为另一种形式的输出。

转换过程的第一步，需要将输入表示为浮点数。之后的


