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

## PyTorch中保存的模型文件.pth深入解析

探究一下，我们通常保存的模型文件 **.pth** 文件内部是什么？

### 一、 .pth 文件详解

如上文所述，在pytorch进行模型保存的时候，一般有两种保存方式，一种是保存这个模型，另一种是只保存模型的参数。保存的模型参数实际上是一个字典类型，通过key-value的形式来存储模型的所有参数。

#### 1.1 .pth 文件基本信息查看。

```python
import torch
pthfile = '/home/workspace/baseline_ckpt.pth'
net = torch.load(pthfile)

print(type(net)) # 类型是dict
print(len(net))  # 长度是4， 即存在4个key-value键值对

for k in net.keys():
	print(k)     # 查看四个键，分别是 model, optimizer, scheduler, iteraion
```

#### 1.2 模型的四个键值详解

##### （1） net['model']

```python
print(net['model'])  # 返回一个OrderedDict对象
for key, value in net['model'].items():
	print(key, values.size(), sep='')
'''  运行结果 '''
module.backbone.body.stem.conv1.weight torch.Size([64, 3, 7, 7])
module.backbone.body.stem.bn1.weight torch.Size([64])
module.backbone.body.stem.bn1.bias torch.Size([64])
module.backbone.body.stem.bn1.running_mean torch.Size([64])
module.backbone.body.stem.bn1.running_var torch.Size([64])
module.backbone.body.layer1.0.downsample.0.weight torch.Size([256, 64, 1, 1])
module.backbone.body.layer1.0.downsample.1.weight torch.Size([256])
.....
```

**总结**：键model 所对应的是值是一个OrderedDict，而这个OrderedDict字典里边又存储着所有的每一层的参数名称以及对应的参数值。

**需要注意的是，**这里参数名称之所以很长，如：

module.backbone.body.stem.conv1.weight
是因为搭建网络结构的时候采用了**组件式的设计**，即整个模型里面构造了一个backbone的容器组件，backbone里面又构造了一个body容器组件，body里面又构造了一个stem容器，stem里面的第一个卷积层的权重。

##### （2）net['optimizer']

```python
# print(net["optimizer"]) # 返回的是一个一般的字典 Dict 对象
for key,value in net["optimizer"].items():
	print(key,type(value),sep=" ")
'''运行结果为 '''
state <class 'dict'>
param_groups <class 'list'>

'''
发现这个这个字典只有两个key，一个是state，一个是param_groups
其中state所对应的值又是一个字典类型，
param_groups对应的值是一个列表
'''
```

先看一下**net["optimizer"] ['param_groups'] **这个列表里面放了一下啥：

```python
groups=net["optimizer"]["param_groups"]
print(groups)
print(len(groups)) # 返回115.即在这个模型中，共有115组

'''
[{'lr': 5.000000000000001e-05, 'weight_decay': 0.0005, 'momentum': 0.9, 'dampening': 0, 'nesterov': False, 'initial_lr': 0.005, 'params': [140566644061240]},
{'lr': 5.000000000000001e-05, 'weight_decay': 0.0005, 'momentum': 0.9, 'dampening': 0, 'nesterov': False, 'initial_lr': 0.005, 'params': [140566644061960]},
{'lr': 5.000000000000001e-05, 'weight_decay': 0.0005, 'momentum': 0.9, 'dampening': 0, 'nesterov': False, 'initial_lr': 0.005, 'params': [140566644062248]},
{'lr': 5.000000000000001e-05, 'weight_decay': 0.0005, 'momentum': 0.9, 'dampening': 0, 'nesterov': False, 'initial_lr': 0.005, 'params': [140566644077336]},
.
.
.
{'lr': 5.000000000000001e-05, 'weight_decay': 0.0005, 'momentum': 0.9, 'dampening': 0, 'nesterov': False, 'initial_lr': 0.005, 'params': [140566644061960]},
{'lr': 5.000000000000001e-05, 'weight_decay': 0.0005, 'momentum': 0.9, 'dampening': 0, 'nesterov': False, 'initial_lr': 0.005, 'params': [140566103171936]},
{'lr': 5.000000000000001e-05, 'weight_decay': 0.0005, 'momentum': 0.9, 'dampening': 0, 'nesterov': False, 'initial_lr': 0.005, 'params': [140566103172008]}]
这个列表的长度为115，每一个元素又是一个字典。
'''
```

再看一下**net["optimizer"] ["states"] **这个字典里面放了啥：

```python
state=net["optimizer"]["state"]
print(len(state)) # 返回115.即在这个模型中，state共有115组

for key,value in state.items():
	print(key,type(value),sep=" ")
'''
140566644061240 <class 'dict'>
140566644061960 <class 'dict'>
140566644062248 <class 'dict'>
140566644077336 <class 'dict'>
.
.
.
140566103171936 <class 'dict'>
140566103172008 <class 'dict'>

这个字典的长度是115，而且和前面的param_groups有着对应关系，每一个元素的键值就是param_groups中每一个元素的params。
'''
```

##### (3)  net ['scheduler']

```python
scheduler=net["scheduler"] # 返回的依然是一个字典
print(len(scheduler)) # 字典的长度为 7
print(scheduler)
'''
{'milestones': (70000, 90000),
'gamma': 0.1,
'warmup_factor': 0.3333333333333333,
'warmup_iters': 500,
'warmup_method': 'linear',
'base_lrs': [0.005, 0.005, 0.005, 0.01, ......, 0.005, 0.005, 0.005, 0.005, 0.01],
'last_epoch': 99999}
继续看一下这个base_lrs的信息
'''

print(len(scheduler["base_lrs"])) # 返回115，→115个数组成的一个列表
```

##### (4) net['iteration']

```
print(net["iteration"]) # 返回 9999 ，它是一个具体的数字
```

