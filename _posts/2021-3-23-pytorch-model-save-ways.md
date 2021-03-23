---
layout: post
comments: true
title: PyTorch 模型保存Tips
excerpt: PyTorch保存模型方式（.pt,.pth,pkl,.p），以及pth方式详解
categories: work
date: 2021-03-23 14:00:00
---

## PyTorch 模型保存方式（.pt, .pth, .pkl, .p）

  最近在投稿论文，准备release code的时候，发现自己的pre-trained model 文件异常的大，竟然有2.5G，就觉得不对劲，要研究一下是不是由于模型保存的时候有什么问题造成的。

### 一、模型不同后缀名的区别

常见的pytorch模型保存的后缀名有**.pt, .pth, .pkl**，其并不是在格式上有区别，只是后缀名不同而已，也仅此而已。在用torch.save()函数保存模型文件时，各人有不同的喜好，有些人喜欢用.pt后缀，有些人喜欢用.pth或.pkl.用相同的torch.save()语句保存出来的模型文件没有什么不同。

在pytorch官方的文档/代码里，有用.pt的，也有用.pth的。一般惯例是使用.pth,但是官方文档里貌似.pt更多，而且官方也不是很在意固定用一种。

### **二、模型保存与调用方式一**

**只保存模型参数，不保存模型结构。**

保存：

```pythonPytho
torch.save(model.state_dict(), mymodel.pth)
# 只保存模型的权重参数，不保存模型结构
```

调用

```python
model = My_model(*args, **kwargs) # 需要重新定义模型结构
model.load_state_dict(torch.load(mymodel.pth)) 
# 这里根据模型结构，调用存储的模型参数
model.eval()
```

### 三、模型保存与调用方式二

保存整个模型，包括模型结构+模型参数

保存

```python
torch.save(model, mymodel.pth) #保存整个model的状态
```

调用

```python
model = torch.load(mymodel.pth)
# 这里不需要重构模型结构，直接load
model.eval()
```



### 四、Example

## PyTorch中保存的模型文件.pth深入解析