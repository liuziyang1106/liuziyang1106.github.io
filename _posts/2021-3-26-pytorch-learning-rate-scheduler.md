---
layout: post
comments: true
title: Pytorch的学习率调整方法
excerpt: 总结一下Pytorch学习率调整方法
categories: work
date: 2021-03-26 12:00:00
---

### 1. lr_scheduler.StepLR

```python
class torch.optim.lr_scheduler.StepLR(optimizer, step_size, gamma=0.1, last_epoch=-1)
```

**Function：**

​	等间隔调整学习率，调整倍数是gamma倍，调整间隔为step_size。间隔单位是step。需要注意的是，step通常是指epoch， 不要指定为batch 了。

**Args：**

- Step_size (int) :学习率下降间隔个数，若为30，则会在30， 60， 90……个step时，将学习率调整为lr * gamma

- gamma (float)：学习率调整倍数，模型为0.1倍，即下降10倍。

- last_epoch (int): 上一个epoch数，这个参数用来指示学习率是否需要调整。当last_epoch符合设定的间隔时，就会对学习率进行调整。当设为-1时，学习率设置为初始值。

  

### 2. lr_scheduler.MultiStepLR

```python
class torch.optim.lr_scheduler.MultiStepLR(optimizer, milestones, gamma=0.1, last_epoch=-1)
```

**Function：**

​	按照设定的间隔调整学习率。这个方法适合后期调试使用，观察loss曲线，为每个实验定制学习率调整时机。

**Args：**

- milestones (list) : 传入一个列表变量，每个元素代表何时调整学习率，其中元素必须是递增的，如 [30, 45, 60]

- gamma (float)：学习率调整倍数，模型为0.1倍，即下降10倍。

- last_epoch (int): 上一个epoch数，这个参数用来指示学习率是否需要调整。当last_epoch符合设定的间隔时，就会对学习率进行调整。当设为-1时，学习率设置为初始值

  

### 3. lr_scheduler.ExponentiaLR

```python
class torch.optim.lr_scheduler.ExponentiaLR(optimizer, gamma=0.1, last_epoch=-1)
```

**Function：**

​	按照指数衰减调整学习率，调整公式为：
$$
lr = lr * gamma ^e
$$


**Args：**

- gamma (float)：学习率调整倍数，模型为0.1倍，即下降10倍。

- last_epoch (int): 上一个epoch数，这个参数用来指示学习率是否需要调整。当last_epoch符合设定的间隔时，就会对学习率进行调整。当设为-1时，学习率设置为初始值。

  

### 4. lr_scheduler.CosineAnnealingLR

```python
class torch.optim.lr_scheduler.CosineAnnealingLR(optimizer, T_max, eta_min=0, last_epoch=-1)
```

**Function：**

​	以余弦函数为周期，并在每个周期最大值时重新设置学习率。[论文《SGDR: Stochastic Gradient Descent with Warm Restarts》(ICLR-2017)](https://arxiv.org/abs/1608.03983)

**Args：**

- T_max (int): 一次学习率周期的迭代次数，即T_max 个epoch后重新设置学习率
- eta_min (float): 最小学习率，即一个周期中，学习率最小会下降到 eta_min，默认值为0
- last_epoch (int): 上一个epoch数，这个参数用来指示学习率是否需要调整。当last_epoch符合设定的间隔时，就会对学习率进行调整。当设为-1时，学习率设置为初始值。



### 5. lr_scheduler.ReduceLROnPlateau

```python
class torch.optim.lr_scheduler.ReduceLROnPlateau(optimizer, mode='min', factor=0.1, patience=10, verbose=False, threshold=0.0001, threshold_mode='rel', cooldown=0, min_lr=0, eps=1e-08)
```

**Function：**

​	当某个指标不再变化（升高或降低），调整学习率。这是非常实用的学习率调整策略。

**Args：**

- mode (str): 模式选择，有min 和max两种模式， min表示当指标不再降低，max表示指标不再升高
- factor(float):学习率调整倍数，等同于上述方法中的gamma
- patience(int): 即忍受该指标多少个step不变化，调整学习率。
- min_lr (float or list): 学习率下限
- eps(float): 学习率衰减的最小值。

### 学习率调整小结

Pytorch提供了六种学习率调整方法，可分为三大类，分别是：

1. 有序调整
2. 自适应调整
3. 自定义调整

第一类，依一定规律有序进行调整，这一类是最常用的，分别是等间隔下降(Step)，
按需设定下降间隔(MultiStep)，指数下降(Exponential)和 CosineAnnealing。这四种方 法的调整时机都是人为可控的，也是训练时常用到的。 

第二类，依训练状况伺机调整，这就是 ReduceLROnPlateau 方法。该法通过监测某一指标的变化情况，当该指标不再怎么变化的时候，就是调整学习率的时机，因而属于自适 应的调整。

第三类，自定义调整，Lambda。Lambda 方法提供的调整策略十分灵活，我们可以为不同的层设定不同的学习率调整方法，这在 fine-tune 中十分有用，我们不仅可为不同的层 设定不同的学习率，还可以为其设定不同的学习率调整策略，简直不能更棒！