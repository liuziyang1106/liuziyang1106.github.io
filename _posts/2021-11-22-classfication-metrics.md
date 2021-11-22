---
layout: post
comments: true
title: 一文理解Acc, Precision, Recall, ROC, AUC
excerpt: Do you know how to calculate classification metrics
categories: work
date: 2021-11-22 16:00:00
---



   从研一开始到现在做了很久的医学影像领域的人工智能工作，主要课题还是集中在疾病的诊断预测、评估预后等方向，对应到深度学习中也就是**分类或者回归任务**。

  对于医疗领域人工智能，在分类任务中 最明显的特点就是数据的 **Class Imbalance** 。常常正样本只占总样本的百分之几甚至千分之几。想想自己要各种评价自己的模型，就要深入的了解对于这种分类任务有哪些评价指标，又该怎么去选择他们，免得被给导师汇报工作的时候被问住，hhh……

 回顾一下，常见的用于分类任务的指标也就那么几个：

- **准确率-- accuracy**
- **精准率-- precision**
- **召回率--recall**
- **F1分数--F1 score**
- **ROC 曲线下面积-- ROC-AUC**
- **PR 曲线下面积-- PR-AUC**



# 使什么是
