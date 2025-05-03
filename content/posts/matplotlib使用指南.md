---
title: matplotlib绘图指南
date: 2023-10-06
categories: ["python"]
---

## 介绍

`matplotlib`是一款非常优秀的python绘图工具，大家在使用python仅绘图时，大部分情况下应该都会想到使用该工具进行绘图，但是`matplotlib`参数众多，画简单的图像可以通过搜索的方式来画出，但是想要画出一张比较复杂的图像，还是有一定门槛的，本文将对matplotlib的绘图做一些介绍说明，希望对大家有所帮助。

## 安装

`matplotlib`的安装非常简单，直接使用`pip`工具安装即可。

```shell
pip install matplotlib
```

> 除了使用`pip`工具直接安装外，还可以通过`conda`、`源码安装`等方式安装`matplotlib`，关于更多的安装方法，可以参考[这里](https://matplotlib.org/stable/users/installing/index.html)。
>

## matplotlib 组件

在具体介绍 `matplotlib` 中各个组件之前，先贴一张官方的介绍图片。

![anatomy](https://littletom.oss-cn-nanjing.aliyuncs.com/anatomy.webp)

这张图片清楚的标识出了一幅图像中的各个组成部分。在 `matplotlib` 中，一幅图像中最核心的元素主要有两个，分别是 `Figure` 和 `Axes`。

`matplotlib` 图像的所有元素都位于一个 `Figure` 对象中，可以把它理解一个最大的容器，用于记录图像中所有元素的信息。一个 `Figure` 中可以包含多个 `Axes`，每个 `Axes` 可以理解为一个子图，它记录了一个子图中的所有元素，包含标题（`title`）、横纵坐标（`axis`）、坐标刻度（`tick`）、横纵坐标标签（`label`）、图像形状（`line`, `scatter`  等）、网格（`grid`）、边框（`spine`）、图例（`legend`）等等。使用 `matplotlib` 绘图的过程可以简单的理解为由两步构成：

1. 创建 `Figure` 对象和 `Axes` 对象；
2. 使用 `Figure` 和 `Axes` 内置的各种方式添加或设置各种元素。

使用下面的代码可以创建 `Figure` 和 `Axes`。

```python
import matplotlib.pyplot as plt
import numpy as np

# 创建一个 Figure 对象，使用这种方式创建完成后，Figure 中未添加任何 Axes
fig = plt.figure()
# 创建一个 Figure 对象，使用这种方式创建完成后，Figure 中包含一个 Axes
fig, ax = plt.subplots()
# 创建一个 Figure 对象，并将其按2行2列的网格结构划分，即包含4个Axes
fig, axs = plt.subplots(2, 2)
# 创建一个 Figure 对象，并将其按左侧1个 Axes，右侧上下2个 Axes 的结构划分，即包含3个Axes
fig, axs = plt.subplot_mosaic([['left', 'right_top'],
                               ['left', 'right_bottom']])
```

## 两种接口风格

在正式了解 `matplotlib` 如何绘制图像之前，有必要先了解一下 `matplotlib` 代码的两种风格，这对理解查看 `matplotlib` 代码非常有帮助。

`matplotlib`在设计时，给使用人员提供了两种接口风格：

1. `Axes` 风格：这是一种**显式**调用 `matplotlib` 接口的风格，使用该风格，你需要自己控制 `Figure` 对象和 `Axes` 对象，并根据自己的需要，在这两个对象上进行操作，是一种标准的“面向对象”实现。
2. `pyplot` 风格：这是一种**隐式**调用  `matplotlib` 接口的风格，是用该风格，你无须自己控制各类对象，`matplotlib`会自动检测你要操作的对象，并执行操作。

> 关于这里的 `Figure` 和 `Axes` 具体是什么，前面已经介绍过了，这里再简单说明一下。可以把它们两个都理解为储存图像各种元素（如标题、横纵坐标、刻度信息等等）的容器。`Figure` 是一幅图像中最大的容器，保存一幅图像所需的所有元素；`Axes` 是一幅子图的最大容器，保存了一幅子图所需的所有元素。对应一个图像中可以包含多个子图，一个 `Figure` 对象中也可以包含若干个 `Axes` 对象。

相信大家在搜索各类资料时，可能也会对这两种风格感到困惑，因为有些文章的代码会使用 `plt.xxx()` ，而有些文章的代码又会使用 `ax.xxx` ，这其实就是使用了不同的风格，只不过文章中没有提到**两种风格**而已。

两种风格各有优略势。`Axes` 风格因为在创建 `Figure` 和 `Axes` 对象时提供了句柄给用户，所以可以很方便地操作图像中的各个细节；`pyplot` 的优势则在于当需要绘制一些非常简单的图像时，会非常的方便，代码会非常简洁。

> 句柄可以简单的理解为就是一个变量，如 `fig, ax = plt.subplots()`，这串代码将实例化后的 `Figure` 对象和 `Axes` 对象保存在了变量 `fig` 和 `ax` 中，我们可以直接通过操作变量实现对图像的操作。
>

在实际工作中，对于一些非常简单的绘图，如只需要一个折线图、柱状图、饼图等地场景，可以直接使用 `pyplot` 风格，此时绘制图像的代码会非常简洁。对于一些略微负责的场景，建议使用 `Axes` 风格，因为它可以手动严格操作图像中的各个小细节，绘图时会更加清晰。倒不是说 `pyplot` 不能绘制复杂的图像，而是在绘制一些较为复杂的图像时，它可能不如 `Axes` 那样方便。

下面通过几个例子来展示一下两种风格的差异。

```python
# Axes风格
import matplotlib.pyplot as plt

fig = plt.figure()
ax = fig.subplots()
ax.plot([1, 2, 3, 4], [0, 0.5, 1, 0.2])
```

```python
# pyplot风格
import matplotlib.pyplot as plt

plt.plot([1, 2, 3, 4], [0, 0.5, 1, 0.2])
```

以上代码都绘制出下面的图像。`pyplot` 风格绘制图像的代码只有一行，而 `Axes` 风格绘制图像的代码却有三行，明显 `pyplot` 的代码更加简洁。但是 `Axes` 代码量虽然多一些，但是它却获取到了 `Figure` 对象和 `Axes` 对象，并都保存到了句柄中，后续如果需要对图像进行更多的处理，它直接使用 `fig` 和 `ax` 即可。

![api_interfaces-1](https://littletom.oss-cn-nanjing.aliyuncs.com/api_interfaces-1.png)

我们再来看一个略微复杂的例子。

```python
# Axes风格
import matplotlib.pyplot as plt

fig, axs = plt.subplots(1, 2)
axs[0].plot([1, 2, 3], [0, 0.5, 0.2])
axs[1].plot([3, 2, 1], [0, 0.5, 0.2])
```

```python
# pyplot风格
import matplotlib.pyplot as plt

plt.subplot(1, 2, 1)
plt.plot([1, 2, 3], [0, 0.5, 0.2])

plt.subplot(1, 2, 2)
plt.plot([3, 2, 1], [0, 0.5, 0.2])
```

上面两段代码，其实都是在绘制下面的图像。与上一个例子不同，这例子中 `Axes` 风格的代码更简洁，这是因为这种风格可以直接获取到两个子图的句柄，而 `pyplot` 则需要通过 `plt.subplot` 命令来切换操作对象。

![api_interfaces-3](https://littletom.oss-cn-nanjing.aliyuncs.com/api_interfaces-3.png)


下面是一个更复杂的例子，更能体现出 `Axes` 风格的优势。

假设现在我们需要对上面的图像加上一个标题，并给每个子图加上横坐标标签，则两种风格绘制图像的代码如下：

```python
# Axes风格
import matplotlib.pyplot as plt

fig, axs = plt.subplots(1, 2)
axs[0].plot([1, 2, 3], [0, 0.5, 0.2])
axs[1].plot([3, 2, 1], [0, 0.5, 0.2])
fig.suptitle('Explicit Interface')
for i in range(2):
    axs[i].set_xlabel('Boo')
```

```python
# pyplot风格
import matplotlib.pyplot as plt

plt.subplot(1, 2, 1)
plt.plot([1, 2, 3], [0, 0.5, 0.2])

plt.subplot(1, 2, 2)
plt.plot([3, 2, 1], [0, 0.5, 0.2])

plt.suptitle('Implicit Interface: re-call subplot')

for i in range(1, 3):
    plt.subplot(1, 2, i)
    plt.xlabel('Boo')
```

可以看出，因为需要来回切换操作对象，`pyplot` 风格的代码变得越来越臃肿。`Axes` 风格在创建对象时就保存了句柄信息，因此即使后续要添加新的细节，仍然只需要在指定对象上操作即可。

![api_interfaces-8](https://littletom.oss-cn-nanjing.aliyuncs.com/api_interfaces-8.png)

对比上面三个例子，建议在实际使用时，尽量使用 `Axes` 风格（面向对象风格），这也是 `matplotlib` 官方所建议的。

> 需要注意的一点是，虽然建议在实际使用时使用 `Axes` 风格，但是它也需要先使用 `plt.figure` 或 `plt.subplots` 的形式先创建 `Figure` 对象和 `Axes` 对象。
>

## 使用 matplotlib 绘图

熟悉了前面的基本概念，下面开始正式介绍使用 `matplotlib` 的绘图方法。

根据我的使用经验，自己整理了一套绘图流程，下面将按照这套绘图流程介绍 `matplotlib` 的绘图方法。这里将按照下面的顺序，由易到难展开介绍：

1. 单子图、单组数据的场景；
2. 单子图、多组数据的场景；
3. 多子图场景。

>
> 这里的**单子图**，顾名思义是指一幅图像中仅包含一个子图。
>
> **单组数据**是指图像中只绘制一组数据序列，形成一个图像，如只包含一条折线的折线图、只包含一组柱的柱状图等。
> 对应的，**多组数据**则是值包含多组数据序列，具体图像如：多条折线的折线图、并列柱状图、同时包含折线和柱的组合图像等。

### 单子图、单组数据场景

单子图、单组数据的场景是一种比较简单的场景，但可以帮助我们梳理清楚一幅图像中要包含哪些元素。这也是绘制多图的基础。具体地，在绘制一幅单子图时，我们可以按照下面地顺序去设计图像和编写代码：

1. 根据数据结构，确定数据的展示形式。
    确定数据展示形式是说，不同的数据对应的最优展示图形是不一样的。一个简单的选择图像原则是：
    - 如果要展示数据的趋势或变化，可以使用**线图**。线图能清晰显示数据随时间的变化。
    - 如果要比较不同类别的数据值，可以使用**柱形图**。柱形图通过长度高低直观对比不同类别的数据。
    - 如果要展示不同分类的数据组成或占比情况，可以使用**饼图**。饼图能一目了然地看出各分类的数据比例。
    - 如果要比较多组数据之间的关系，可以使用**散点图**。散点图通过点的分布看出数据之间的相关性。
    - 如果数据包含多个维度，可以使用**雷达图**。雷达图能同时比较多个指标的数据情况。
    - 如果要突出展示极大或极小值，可以使用**箱线图**。箱线图能标识出异常数据点。
    
2. 创建画布和 `Axes`；

   ```python
   import matplotlib.pyplot as plt
   fig = plt.figure()
   ax = fig.subplots()
   ```

3. 根据选择地图像类型，整理数据，并绘制对应的形状。
   
   ```python
   x = [1,2,3,4]
   y = [0, 0.5, 1, 0.2]
   ax.plot(x, y)  # 绘制折线图
   ```
   
   各种形状对应的方法如下：
   |方法|形状|
   |:-:|:-:|
   |`plot`|线图|
   |`scatter`|散点图|
   |`bar`|柱状图|
   |`hist`|直方图|
   |`boxplot`|箱线图|
   |`pie`|饼图|
   |`violinplot`|小提琴图|
   |`fill_between`|置信度曲线图|
   |`stackplot`|堆积图|
   |`step`|跃迁图|

   更多的形状绘制方法以及各个方法详细的参数信息可以参考[这里](https://matplotlib.org/stable/plot_types/)。

4. 设置标题；
   
   ```python
   ax.set_title('标题')
   ```

5. 设置坐标轴；
    坐标轴设置包含许多内容，可以先将坐标轴分为两部分：横坐标和纵坐标。横纵坐标包含的内容时相同的，这里为了介绍方便，只介绍横坐标需要设置的内容，纵坐标的设置是类似的。

    横坐标需要设置的内容包括：
    - `label`: 坐标轴说明
    - `major tick`: 主坐标轴
    - `minor tick`: 次坐标轴   

6. 设置网格线。