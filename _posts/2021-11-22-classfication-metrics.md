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



## **一、混淆矩阵，准确率，精准率，召回率**

### 1.1 混淆矩阵

在介绍各个率之前，先来介绍一下混淆矩阵。如果我们用的是个二分类的模型，那么把预测情况与实际情况的所有结果两两混合，结果就会出现以下4种情况，就组成了**混淆矩阵**。

|              |      | 真实标签 |      |
| ------------ | ---- | -------- | ---- |
|              |      | 1        | 0    |
| **预测结果** | 1    | TP       | FP   |
|              | 0    | FN       | TN   |

**P（Positive）：**代表1

**N（Negative）：**代表0

**T（True）：**代表预测正确

**F（False）：**代表错误

------

**根据混淆矩阵就可以**

- **TP--True positive：**预测为1，预测正确，即实际1

- **FP--False positive：**预测为1，预测错误，即实际0

- **FN--False negative：**预测为0，预测错误，即实际1

- **TN--True negative：**预测为0，预测正确即，实际0

### 1.2 准确率

既然是个分类指标，我们可以很自然的想到**准确率，**准确率的定义是**预测正确的结果占总样本的百分比**，其公式如下：


$$
Acc = \frac {TP+TN}{TP+TN+FP+FN}
$$


**但在binary classification 且正反例不平衡的情况下，尤其是我们对 少数类 minority class 更感兴趣的时候，accuracy评价基本没有参考价值。**什么fraud detection（欺诈检测），癌症检测，都符合这种情况。举个例子：

在测试集里，有100个sample，99个反例，只有1个正例。如果我的模型不分青红皂白对任意一个sample都预测是反例，那么我的模型的accuracy是 正确的个数／总个数 = 99/100 = 99%。**由于样本不平衡的问题，导致了得到的高准确率结果含有很大的水分。即如果样本不平衡，准确率就会失效**

这种现象被称作：**accuracy paradox。**

正因为如此，也就衍生出了其它两种指标：**精准率和召回率。**

### 1.3 精准率 Precision

精准率（Precision）又叫**查准率**，它是**针对预测结果**而言的，它的含义是**在所有被预测为正的样本中实际为正的样本的概率**，意思就是在预测为正样本的结果中，我们有多少把握可以预测正确，其公式如下：


$$
Precision = \frac {TP}{TP+FP}
$$




> 精准率和准确率看上去有些类似，但是完全不同的两个概念。精准率代表对正样本结果中的预测准确程度，而准确率则代表整体的预测准确程度，既包括正样本，也包括负样本。

### 1.4 召回率 Recall

召回率（Recall）又叫**查全率**，它是**针对原样本**而言的，它的含义是**在实际为正的样本中被预测为正样本的概率**，其公式如下：


$$
Recall = \frac {TP}{TP+FN}
$$


统一起来的理解呢，就是，

- recall是相对真实的答案而言： true positive ／ golden set 。假设测试集里面有100个正例，你的模型能预测覆盖到多少，如果你的模型预测到了40个正例，那你的recall就是40%。
- precision是相对你自己的模型预测而言：true positive ／retrieved set。假设你的模型一共预测了100个正例，而其中80个是对的正例，那么你的precision就是80%。我们可以把precision也理解为，当你的模型作出一个新的预测时，它的confidence score 是多少，或者它做的这个预测是对的的可能性是多少。
- 一般来说呢，鱼与熊掌不可兼得。如果你的模型很贪婪，想要覆盖更多的sample，那么它就更有可能犯错。在这种情况下，你会有很高的recall，但是较低的precision。如果你的模型很保守，只对它很sure的sample作出预测，那么你的precision会很高，但是recall会相对低。

**这样一来呢，我们可以选择只看我们感兴趣的class，就是minority class的precision，recall来评价模型的好坏。**

### 1.5 F1 分数 F1-Score

通过上面的公式，我们发现：精准率和召回率的分子是相同，都是TP，但分母是不同的，一个是（TP+FP），一个是（TP+FN）。
$$
F1 = \frac{2 \times Precision \times Recall}{Precision + Recall}
$$
F1-score 就是一个综合考虑precision和recall的评价指标

## **二、ROC-AUC，Sensitivity， Specificity**

我们还要再介绍两个指标，**这两个指标的选择也正是ROC和AUC可以无视样本不平衡的原因。**这两个指标分别是：**灵敏度和（1-特异度），也叫做真正率（TPR）和假正率（FPR）**。

灵敏度（Sensitivity） = **TP/(TP+FN)**

特异度（Specificity） = **TN/(FP+TN)**



- 其实我们可以发现灵敏度和召回率是一模一样的，只是名字换了而已。
- 由于我们比较关心正样本，所以需要查看有多少负样本被错误地预测为正样本，所以使用（1-特异度），而不是特异度。



真阳率（TPR） = 灵敏度 = **TP/(TP+FN)**

假阳率（FPR） = 1- 特异度 = **FP/(FP+TN)**



下面是真正率和假正率的示意，我们发现**TPR和FPR分别是基于实际表现1和0出发的，也就是说它们分别在实际的正样本和负样本中来观察相关概率问题。**正因为如此，所以无论样本是否平衡，都不会被影响。还是拿之前的例子，总样本中，90%是正样本，10%是负样本。我们知道用准确率是有水分的，但是用TPR和FPR不一样。这里，TPR只关注90%正样本中有多少是被真正覆盖的，而与那10%毫无关系，同理，FPR只关注10%负样本中有多少是被错误覆盖的，也与那90%毫无关系，所以可以看出：**如果我们从实际表现的各个结果角度出发，就可以避免样本不平衡的问题了，这也是为什么选用TPR和FPR作为ROC/AUC的指标的原因。**

我们还要再介绍两个指标，**这两个指标的选择也正是ROC和AUC可以无视样本不平衡的原因。**这两个指标分别是：**灵敏度和（1-特异度），也叫做真正率（TPR）和假正率（FPR）**。

灵敏度（Sensitivity） = **TP/(TP+FN)**

特异度（Specificity） = **TN/(FP+TN)**



- 其实我们可以发现灵敏度和召回率是一模一样的，只是名字换了而已。
- 由于我们比较关心正样本，所以需要查看有多少负样本被错误地预测为正样本，所以使用（1-特异度），而不是特异度。

<div class="imgcap">
<img src="/assets/2021-11-22-ROC-curve.gif">
<div class="thecap"></div>
</div>



### **AUC（曲线下的面积）**

为了计算 ROC 曲线上的点，我们可以使用不同的分类阈值多次评估逻辑回归模型，但这样做效率非常低。幸运的是，有一种基于排序的高效算法可以为我们提供此类信息，这种算法称为**曲线下面积（Area Under Curve）**。



比较有意思的是，如果我们连接对角线，它的面积正好是0.5。对角线的实际含义是：**随机判断响应与不响应，正负样本覆盖率应该都是50%，表示随机效果。**ROC曲线越陡越好，所以理想值就是1，一个正方形，而最差的随机判断都有0.5，所以一般AUC的值是介于0.5到1之间的。



**AUC的一般判断标准**

**0.5 - 0.7：**效果较低，但用于预测股票已经很不错了

**0.7 - 0.85：**效果一般

**0.85 - 0.95：**效果很好

**0.95 - 1：**效果非常好，但一般不太可能



**AUC的物理意义**

> 曲线下面积对所有可能的分类阈值的效果进行综合衡量。曲线下面积的一种解读方式是看作模型将某个随机正类别样本排列在某个随机负类别样本之上的概率。以下面的样本为例，逻辑回归预测从左到右以升序排列：

<div class="imgcap">
<img src="/assets/2021-11-22-aus-intetp.png">
<div class="thecap"></div>
</div>
