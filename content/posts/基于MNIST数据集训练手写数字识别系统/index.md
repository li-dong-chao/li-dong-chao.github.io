---
title: 基于MNIST数据集训练手写数字识别系统
date: 2024-03-15
categories: ["机器学习"]
tags: ["PyTorch"]
---

`MNIST` 是一个免费的开源数据集，由 70000 张 $28 \times 28$ 像素的黑白图片构成，其 60000 张为训练样本，10000张为测试样本。该数据集可用于训练手写数字识别模型，通常作为机器学习、深度学习的入门数据集使用。

![image-20240315232640408](https://littletom.oss-cn-nanjing.aliyuncs.com/image-20240315232640408.png)

本文尝试使用 `pytorch` 搭建一个简单的神经网络模型，并在 `MNIST` 数据集上进行训练，最终实现手写数字的识别。

> 本文主要介绍如何用 `pytorch` 实现整个流程，至于理论和最终的效果表现不是文本的重点。

## 数据获取与数据加载

`torchvision` 中集成了 `MNIST` 数据集，可以直接使用该框架来获取数据，获取数据的python代码如下：

```python
from torch.utils.data import DataLoader
from torchvision.datasets import MNIST
from torchvision import transforms
from torch.utils.tensorboard import SummaryWriter

# 获取训练集数据
train_data = MNIST(root='../data', train=True, download=True, transform=transforms.ToTensor())
# 获取测试集数据
test_data = MNIST(root='../data', train=False, download=True, transform=transforms.ToTensor())

# 使用dataloader进行数据加载
data_loader = DataLoader(train_data, batch_size=64, shuffle=True)

# 使用tensorboard实现图片数据展示
with SummaryWriter(log_dir='../logs') as writer:
    for i, (imgs, targets) in enumerate(data_loader):
        writer.add_images("train", imgs, i)
```

执行上面的代码，程序将会自动下载 `MNIST` 数据集。在 `MNIST` 的各个参数中， `root` 参数表示数据的存储路径，下载的数据将会保存至该目录下；`train` 参数表示是否下载训练集，当取值为 `True` 时，程序会下载训练集数据，否则下载测试集数据；`download` 参数表示是否自动下载数据集，当 `root` 目录下不存在数据时，程序会自动从互联网下载数据；`transform` 参数表示要对数据进行的后处理，这里的 `transforms.ToTensor()` 表示将数据转化为 `tensor` 对象。

上面代码中，除了获取数据还构造了一个 `dataloader` ，用于实现对训练集（或测试集）数据的批次取样，上面代码中的 `batch_size=64` 表示数据将以每批64张图片的形式进行取样，`shuffle=True` 则表示在取样前会对数据进行随机打乱。

代码的最后三行主要作用是利用 `tensorboard` 实现图片数据的可视化展示，其中 `log_dir` 指定了 `tensorboard` 缓存数据的目录，`writer.add_images` 方法则会添加数据到 `tensorboard` 的数据展示面板。执行完上述代码后，在命令行窗口执行 `tensorboard --logdir="../logs"` 便能够启动数据展示面板，形式如下。通过该面板，可以对数据集进行初步的查看。

![image-20240317151929804](https://littletom.oss-cn-nanjing.aliyuncs.com/image-20240317151929804.png)

## 数据结构查看

获取到数据后，需要对数据的基本结构进行了解，包括数据集的大小以及图片的通道数、像素大小等。代码如下：

```python
>>> print(f"训练集样本数量：{len(train_data)}")
训练集样本数量：60000

>>> print(f"测试集样本数量：{len(test_data)}")
测试集样本数量：10000

>>> print(f"图片结构：{train_data[0][0].shape}")
图片结构：torch.Size([1, 28, 28])
```

可以看出训练集数据为60000，测试集数据为10000，图片只有1个通道，图片的尺寸为 $28 \times 28$ .

> 在实际分析过程中，首先查看 `train_data[0]` ，发现该对象为一个 `tuple` ，第一个元素为图片的 `tensor` 对象，第二个为图片标签，所以在查看图片结构时查看 `train_data[0][0]` 的 `shape` 属性。

## 创建神经网络模型

使用 `pytorch` 框架可以很方便的创建机器学习模型，其 `nn` 模块内置了神经网络、深度学习常用的多种连接层，具体包含哪些连接层，可以从 `pytorch` [官网](https://pytorch.org/docs/stable/nn.html) 查看。除了各种连接层外，`nn` 模块提供的 `Sequential` 方法还支持将多个连接层连接到一起，创建一个网络结构。

本文基于 `nn` 模块创建了一个简单的神经网络模型，它由3个线性层、2个 `ReLU` 激活层以及1个 `softmax` 层构成，该网络结构的构建代码如下：

```python
from torch import nn


class MyNetwork(nn.Module):  # 从 nn.Module 继承，创建一个模型
    def __init__(self):
        super().__init__()
        self.model = nn.Sequential(  # model属性记录模型的结构，具体哪些结构可以直接从 nn 模块获取，这里只是随便搭建了一个网络
            nn.Flatten(),
            nn.Linear(28 * 28, 64),
            nn.ReLU(),
            nn.Linear(64, 32),
            nn.ReLU(),
            nn.Linear(32, 10),
            nn.Softmax(dim=1)  # 由于数据标签为多分类，模型最后使用 softmax 实现多分类
        )

    def forward(self, x):
        output = self.model(x)
        return output

```

上面的网络结构中，由于 `MNIST` 数据集中图片大小为 $28 \times 28$，所以第一个线性层的输入元素参数必须为 `28*28`；由于手写数字识别的标签为 `0-9` 共10个数字，所以最后一个线性层的输出元素参数必须为 `10` 。除此之外，所有线性层参数均为超参数，可以根据模型表现进行相应的调整。

> 本文只是为了梳理整体训练流程，上面的结构只是随便构建的，其结构及参数情况可能并不是最优的。事实上，使用简单的神经网络来实现手写数字识别，效果相比于深度学习中 `CNN` 等网络结构要差许多，如果读者比较关注最终的效果表现，请尝试使用其他的网络结构。

结合 `tensorboard` 工具，可以对网络结构进行可视化，代码如下：

```python
import torch
from torch import nn


class MyNetwork(nn.Module):
    def __init__(self):
        super().__init__()
        self.model = nn.Sequential(
            nn.Flatten(),
            nn.Linear(28 * 28, 64),
            nn.ReLU(),
            nn.Linear(64, 64),
            nn.ReLU(),
            nn.Linear(64, 10),
            nn.Softmax(dim=1)
        )

    def forward(self, x):
        output = self.model(x)
        return output


if __name__ == '__main__':
    from torch.utils.tensorboard.writer import SummaryWriter
    model = MyNetwork()
    sample = torch.randn(1, 28 * 28)
    with SummaryWriter("./logs/") as writer:
        writer.add_graph(model, sample)

```

执行完上面的代码之后，在命令行窗口执行 `tensorboard --logdir="./logs` 便可以查看网络结构了，形式如下：

![image-20240318130937160](https://littletom.oss-cn-nanjing.aliyuncs.com/image-20240318130937160.png)

## 训练模型

下面介绍如何基于 `pytorch` 实现网络的训练。

### 加载数据

训练数据时首先需要加载数据，加载方式与前面介绍加载 `MNIST` 数据相同，这里直接贴上代码，不进行赘述介绍。

```python
import torch
import torchvision
from torch.utils.data import DataLoader


# 加载数据
train_data = torchvision.datasets.MNIST(
    root='../data',
    train=True,
    download=True,
    transform=torchvision.transforms.ToTensor(),
)
test_data = torchvision.datasets.MNIST(
    root='../data',
    train=False,
    download=True,
    transform=torchvision.transforms.ToTensor()
)

# 计算训练集和测试集的样本量
train_size = len(train_data)
test_size = len(test_data)

# 构造数据加载器，实现数据的批量获取
train_dataloader = DataLoader(train_data, batch_size=64)
test_dataloader = DataLoader(test_data, batch_size=64)
```

### 损失函数

手写数字识别是一个典型的多分类任务，针对多分类任务，经常会采用 **交叉损失函数** 来作为损失函数。在 `pytorch` 中可以使用 `CrossEntropyLoss` 来实现交叉损失函数的计算。

```python
from torch.nn import CrossEntropyLoss

# 构建交叉熵损失计算器
loss_fn = CrossEntropyLoss()
```

### 优化方法

确定损失的计算方式后，还需要进一步确定使用何种最优化方案来实现损失最小化，或者说实现对模型参数的优化。本文直接使用 **梯度下降法** 作为最优解的求解方法。在 `pytorch` 中创建最优化迭代器的代码如下：

```python
from torch.optim import SGD

# 学习率
learning_rate = 0.005
# 创建最优化迭代器
optimizer = SGD(model.parameters(), lr=learning_rate)
# 这里的model为自己定义网络结构类后，实例化的对象，对应上面构建网络的代码，即为 model = MyNetwork()
```

将上面的代码进行整合，便能够得到最终的训练代码。

```python
#!/usr/bin/env python
# -*- encoding: utf-8 -*-
import torch
import torchvision
from torch.nn import CrossEntropyLoss
from torch.optim import SGD
from torch.nn.functional import one_hot
from torch.utils.data import DataLoader
from torch.utils.tensorboard import SummaryWriter

from app.model.network import MyNetwork

# 加载数据
train_data = torchvision.datasets.MNIST(
    root='../data',
    train=True,
    download=True,
    transform=torchvision.transforms.ToTensor(),
)
test_data = torchvision.datasets.MNIST(
    root='../data',
    train=False,
    download=True,
    transform=torchvision.transforms.ToTensor()
)

train_size = len(train_data)
test_size = len(test_data)

train_dataloader = DataLoader(train_data, batch_size=64)
test_dataloader = DataLoader(test_data, batch_size=64)

# 使用tensorboard来记录训练过程
writer = SummaryWriter('../log')

total_train_step = 0
epoch = 100
learning_rate = 0.005

model = MyNetwork()
# 创建优化器
optimizer = SGD(model.parameters(), lr=learning_rate)
# 创建损失
loss_fn = CrossEntropyLoss()

# 开始训练
for i in range(1, epoch + 1):
    print(f"第 {i} 轮训练开始".center(20, "-"))

    # 开始训练
    total_loss = 0
    model.train()
    for data in train_dataloader:
        imgs, targets = data
        targets = one_hot(targets, 10).float()
        output = model(imgs)
        loss = loss_fn(output, targets)

        # 反向传播
        optimizer.zero_grad()
        loss.backward()
        optimizer.step()

        total_train_step += 1
        total_loss += loss.item()

        if total_train_step % 100 == 0:
            print(f"训练次数：{total_train_step}，Loss：{loss.item()}")
        writer.add_scalar("total_train_step vs train_loss", loss.item(), total_train_step)

    writer.add_scalar("epoch vs train_loss", total_loss, i)

    # 测试
    model.eval()
    total_right_num = 0
    with torch.no_grad():
        for data in test_dataloader:
            imgs, targets = data
            output = model(imgs)
            right_num = sum(output.argmax(1) == targets)
            total_right_num += right_num
    test_accuracy = total_right_num / test_size
    print(f"测试集准确率：{test_accuracy}")
    writer.add_scalar("epoch vs test_loss", test_accuracy, i)

# 保存模型
torch.save(model.state_dict(), "../model/model.pth")

writer.close()
```
