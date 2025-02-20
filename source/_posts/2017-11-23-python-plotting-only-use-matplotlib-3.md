---
layout: post
title:  "Python 绘图,我只用 Matplotlib(三)"
date:   2017-11-23 11:07:19
urlname: 25
cover: https://img.jikehou.cn/cover/2017-11-23.jpg
categories: [数据分析]
tags: [Python, 数据分析, Matplotlib]
keywords: [Python, 数据分析, Matplotlib, 数据可视化]
---
上篇文章，我已经讲解绘制图像大致步骤，接下来的系列文章将分别对各种图形做讲解。其实就是了解各个图种的绘图 API。文章就讲解第一种图形，柱状图。

## 1.基础
绘制柱状图，我们主要用到`bar()`函数。只要将该函数理解透彻，我们就能绘制各种类型的柱状图。
<!-- more -->
我们先看下`bar()`的构造函数：`bar(x，height， width，*，align='center'，**kwargs)`

- x
包含所有柱子的下标的列表

- height
包含所有柱子的高度值的列表

- width
每个柱子的宽度。可以指定一个固定值，那么所有的柱子都是一样的宽。或者设置一个列表，这样可以分别对每个柱子设定不同的宽度。

- align
柱子对齐方式，有两个可选值：`center`和`edge`。`center`表示每根柱子是根据下标来对齐, `edge`则表示每根柱子全部以下标为起点，然后显示到下标的右边。如果不指定该参数，默认值是`center`。

其他可选参数有：
- color
每根柱子呈现的颜色。同样可指定一个颜色值，让所有柱子呈现同样颜色；或者指定带有不同颜色的列表，让不同柱子显示不同颜色。

- edgecolor
每根柱子边框的颜色。同样可指定一个颜色值，让所有柱子边框呈现同样颜色；或者指定带有不同颜色的列表，让不同柱子的边框显示不同颜色。

- linewidth
每根柱子的边框宽度。如果没有设置该参数，将使用默认宽度，默认是没有边框。

- tick_label
每根柱子上显示的标签，默认是没有内容。

- xerr
每根柱子顶部在横轴方向的线段。如果指定一个固定值，所有柱子的线段将一直长；如果指定一个带有不同长度值的列表，那么柱子顶部的线段将呈现不同长度。

- yerr
每根柱子顶端在纵轴方向的线段。如果指定一个固定值，所有柱子的线段将一直长；如果指定一个带有不同长度值的列表，那么柱子顶部的线段将呈现不同长度。

- ecolor
设置 xerr 和 yerr 的线段的颜色。同样可以指定一个固定值或者一个列表。

- capsize
这个参数很有趣, 对`xerr`或者`yerr`的补充说明。一般为其设置一个整数，例如 10。如果你已经设置了 
yerr 参数，那么设置 capsize 参数，会在每根柱子顶部线段上面的首尾部分增加两条垂直原来线段的线段。对 xerr 参数也是同样道理。可能看说明会觉得绕，如果你看下图就一目了然了。

![运行结果](https://img.jikehou.cn/img/20171123_1.png)

- error_kw
设置 xerr 和 yerr 参数显示线段的参数，它是个字典类型。如果你在该参数中又重新定义了 ecolor 和 capsize，那么显示效果以这个为准。

- log
这个参数，我暂时搞不懂有什么用。

- orientation
设置柱子是显示方式。设置值为 vertical ，那么显示为柱形图。如果设置为 horizontal 条形图。不过 matplotlib 官网不建议直接使用这个来绘制条形图，使用`barh`来绘制条形图。



下面我就调用 bar 函数绘制一个最简单的柱形图。
```python
import matplotlib.pyplot as plt
import numpy as np

# 创建一个点数为 8 x 6 的窗口, 并设置分辨率为 80像素/每英寸
plt.figure(figsize=(8, 6), dpi=80)

# 再创建一个规格为 1 x 1 的子图
plt.subplot(1, 1, 1)

# 柱子总数
N = 6
# 包含每个柱子对应值的序列
values = (25, 32, 34, 20, 41, 50)

# 包含每个柱子下标的序列
index = np.arange(N)

# 柱子的宽度
width = 0.35

# 绘制柱状图, 每根柱子的颜色为紫罗兰色
p2 = plt.bar(index, values, width, label="rainfall", color="#87CEFA")

# 设置横轴标签
plt.xlabel('Months')
# 设置纵轴标签
plt.ylabel('rainfall (mm)')

# 添加标题
plt.title('Monthly average rainfall')

# 添加纵横轴的刻度
plt.xticks(index, ('Jan', 'Fub', 'Mar', 'Apr', 'May', 'Jun'))
plt.yticks(np.arange(0, 81, 10))

# 添加图例
plt.legend(loc="upper right")

plt.show()
```

运行结果为：
![运行结果](https://img.jikehou.cn/img/20171123_2.png)


## 2.进阶
bar 函数的参数很多，你可以使用这些参数绘制你所需要柱形图的样式。

如果你还不会灵活使用这样参数，那就让我们来学习 matplotlib 官方提供的例子。
```python
# Credit: Josh Hemann

import numpy as np
import matplotlib.pyplot as plt
from matplotlib.ticker import MaxNLocator
from collections import namedtuple

n_groups = 5

means_men = (20, 35, 30, 35, 27)
std_men = (2, 3, 4, 1, 2)

means_women = (25, 32, 34, 20, 25)
std_women = (3, 5, 2, 3, 3)

fig, ax = plt.subplots()

index = np.arange(n_groups)
bar_width = 0.35

opacity = 0.4
error_config = {'ecolor': '0.3'}

rects1 = ax.bar(index, means_men, bar_width,
                alpha=opacity, color='b',
                yerr=std_men, error_kw=error_config,
                label='Men')

rects2 = ax.bar(index + bar_width, means_women, bar_width,
                alpha=opacity, color='r',
                yerr=std_women, error_kw=error_config,
                label='Women')

ax.set_xlabel('Group')
ax.set_ylabel('Scores')
ax.set_title('Scores by group and gender')
ax.set_xticks(index + bar_width / 2)
ax.set_xticklabels(('A', 'B', 'C', 'D', 'E'))
ax.legend()

fig.tight_layout()
plt.show()
```

运行结果如下：
![运行结果](https://img.jikehou.cn/img/20171123_3.png)


开动你的大脑，想想还能绘制出什么样式的柱形图。