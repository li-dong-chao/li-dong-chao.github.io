---
title: 基于matplotlib.animation的动态绘图方法
date: 2023-08-07
categories: ["python"]
---

matplotlib是python中最基本，也是最常用的画图工具。
利用matplotlib不仅可以绘制各种各样的图片，还可以制作一些小动画，下面就来介绍一下matplotlib如何制作动画。

动画的制作是基于matplotlib的animation，对于一条线的绘制，网上的教程很多，这里不介绍，主要介绍一下一图多线的情况。

```python
# 导入相关库
import seaborn as sns
import matplotlib.pyplot as plt
import matplotlib.animation as animation

# 生成数据
x1 = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
y1 = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
x2 = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
y2 = [0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 0.10]

# 简单设置一下绘图基本参数
sns.set_style('darkgrid')

# 绘图
fig, ax = plt.subplots()
ax.set_title('draw lines')
ax.set_xlabel('x')
ax.set_ylabel('y')
ln1, =ax.plot([], [], 'r-o', animated=False)
ln2, =ax.plot([], [], 'r-o', animated=False)
ax.legend(['y1, y2'], loc='upper left')

def update(frame):
    x_1 = x1[0:frame]
    y_1 = x1[0:frame]
    x_2 = x1[0:frame]
    y_2 = x1[0:frame]
    ln1.set_data(x_1, y_1)
    ln2.set_data(x_2, y_2)
    return ln1, ln2,

ani = animation.FuncAnimation(fig, update, frames=len(x1)+1, interval=300, blit=True)

plt.show()
```

绘制的图像如下：

![20210825_1](https://littletom.oss-cn-nanjing.aliyuncs.com/20210825_1.gif)
