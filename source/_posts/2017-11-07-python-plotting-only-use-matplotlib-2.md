---
layout: post
title:  "Python 绘图,我只用 Matplotlib(二)"
date:   2017-11-07 17:34:31
urlname: 23
cover: https://img.jikehou.cn/cover/2017-11-07.jpg
categories: [数据分析]
tags: [Python, 数据分析, Matplotlib]
keywords: [Python, 数据分析, Matplotlib, 数据可视化]
---
上篇文章，我们了解到 Matplotlib 是一个风格类似 Matlab 的基于 Python 的绘图库。

它提供了一整套和matlab相似的命令API，十分适合交互式地进行制图。

而且我们也可以方便地将它作为绘图控件，嵌入GUI应用程序中。

本文主要走进 Matplotlib 的世界，初步学会绘制图形。
<!-- more -->

## 1.基础知识
在学习绘制之前，先来了解下 Matplotlib 基础概念。

### 1-1.库
我们绘制图形主要用到两个库，`matplotlib.pyplot`和`numpy`。

在编码过程中，这两个库的使用频率较高，而这两个库的名字较长。

这难免会给我们带来不便。所以我们一般给其设置别名， 大大减少重复性工作量。具体如下代码：
```python
import matplotlib.pyplot as plt   # 导入模块 matplotlib.pyplot，并简写成 plt 
import numpy as np                # 导入模块 numpy，并简写成 np
```
numpy 是 Python 用于数学运算的库，它是在安装 matplotlib 时候顺带安装的。

pyplot 是 matplotlib 一个子模块，主要为底层的面向对象的绘图库提供状态机界面。

状态机隐式地自动创建数字和坐标轴以实现所需的绘图。 matplotlib 中的所有内容都按照层次结果进行组织。

顶层就是由 pyplot 提供的 matplotlib “状态机环境”。

基于这个状态机环境，我们就可以创建图形。

### 1-2.图形组成标签
我在 matplotlib 官网上找图像组件说明图并在上面增加中文翻译。

通过这张图，我们对 matplotlib 整体地认识。

![我们对 matplotlib 整体地认识](https://img.jikehou.cn/img/20171107_1.jpg)

接下来，我主要讲解 matplotlib 中几个重要的标签。

- Figure

Figure 翻译成中文是图像窗口。Figure 是包裹 Axes、tiles、legends 等组件的最外层窗口。它其实是一个 Windows 应用窗口 。
![Figure](https://img.jikehou.cn/img/20171107_2.png)
Figure 中最主要的元素是 Axes（子图）。一个 Figure 中可以有多个子图，但至少要有一个能够显示内容的子图。

-  Axes 

Axes 翻译成中文是轴域/子图。Axes 是带有数据的图像区域。从上文可知，它是位于 Figure 里面。那它和 Figure 是什么关系？这里可能文字难以表述清楚，我以图说文。用两图带你彻底弄清它们的关系。

在看运行结果之前，我先呈上代码给各位看官品尝。
```python
fig = plt.figure()                      # 创建一个没有 axes 的 figure
fig.suptitle('No axes on this figure')  # 添加标题以便我们辨别

fig, ax_lst = plt.subplots(2, 2)        # 创建一个以 axes 为单位的 2x2 网格的 figure 
plt.show()
```
根据运行结果图，我们不难看出。左图的 Figure1 中没有 axes，右图的 Figure2 中有 4 个 axes。因此，我们可以将 Axes 理解为面板，而面板是覆在窗口(Figure) 上。 
![Axes](https://img.jikehou.cn/img/20171107_3.png)

- Axis 

Axis 在中文的意思是轴。官网文档对 Axis 定义解释不清楚，让我们看得云里雾里的。如果你有留意前文的组成说明图，可以看到 X Axis 和 Y Axis 的字样。按照平常人的见识， 观察该图就能明白 Axis 是轴的意思。此外，Axis 和 Axes 以及 Figure 这三者关系，你看完下图，会恍然大悟。
![Axis](https://img.jikehou.cn/img/20171107_4.png)

## 2.绘制第一张图
按照剧本发展，我接下来以绘制曲线并逐步美化它为例子，一步步讲解如何绘制图形。在这过程中，我也会逐一说明各个函数的作用。

### 2-1.初步绘制曲线

```python
import matplotlib.pyplot as plt
import numpy as np

x = np.linspace(-2, 6, 50)
y1 = x + 3      # 曲线 y1
y2 = 3 - x      # 曲线 y2
plt.figure()    # 定义一个图像窗口
plt.plot(x, y1) # 绘制曲线 y1
plt.plot(x, y2) # 绘制曲线 y2
plt.show()
```

调用`np.linspace`是创建一个 numpy 数组，并记作 x。x 包含了从 -2 到 6 之间等间隔的 50 个值。

y1 和 y2 则分别是这 50 个值对应曲线的函数值组成的 numpy 数组。

前面的操作还处于设置属性的阶段，还没有开始绘制图形。`plt.figure()` 函数才意味着开始执行绘图操作。

最后别忘记调用`show()`函数将图形呈现出来。

![呈现结果](https://img.jikehou.cn/img/20171107_5.png)

### 2-2.简单修饰
我们已经绘制出两条直线，但样式比较简陋。

所以我给两条曲线设置鲜艳的颜色、线条类型。同时，还给纵轴和横轴的设置上下限，增加可观性。
```python
import matplotlib.pyplot as plt
import numpy as np

# 创建一个点数为 8 x 6 的窗口, 并设置分辨率为 80像素/每英寸
plt.figure(figsize=(8, 6), dpi=80)

# 再创建一个规格为 1 x 1 的子图
plt.subplot(1，1，1)

x = np.linspace(-2, 6, 50)
y1 = x + 3        # 曲线 y1
y2 = 3 - x        # 曲线 y2

# 绘制颜色为蓝色、宽度为 1 像素的连续曲线 y1
plt.plot(x, y1, color="blue", linewidth=1.0, linestyle="-")
# 绘制颜色为紫色、宽度为 2 像素的不连续曲线 y2
plt.plot(x, y2, color="#800080", linewidth=2.0, linestyle="--")

# 设置横轴的上下限
plt.xlim(-1, 6)
# 设置纵轴的上下限
plt.ylim(-2, 10)

plt.show()
```

![简单修饰](https://img.jikehou.cn/img/20171107_6.png)

### 2-3.设置纵横轴标签
在图像中，我们不能一味地认为横轴就是 X 轴，纵轴就是 Y 轴。图形因内容数据不同，纵横轴标签往往也会不同。这也体现了给纵横轴设置标签说明的重要性。
```python
...
# 设置横轴标签
plt.xlabel("X")
# 设置纵轴标签
plt.ylabel("Y")

plt.show()
```

![设置纵横轴标签](https://img.jikehou.cn/img/20171107_7.png)

### 2-4.设置精准刻度
matplotlib 画图设置的刻度是由曲线以及窗口的像素点等因素决定。

这些刻度精确度无法满足需求，我们需要手动添加刻度。

上图中，纵轴只显示 2 的倍数的刻度，横轴只显示 1 的倍数的刻度。

我们为其添加精准刻度，纵轴变成单位间隔为 1 的刻度，横轴变成单位间隔为 0.5 的刻度。

```python
...
# 设置横轴精准刻度
plt.xticks([-1, -0.5, 0, 0.5, 1, 1.5, 2, 2.5, 3, 3.5, 4, 4.5, 5, 5.5])
# 设置纵轴精准刻度
plt.yticks([-2, -1, 0, 1, 2, 3, 4, 5, 6, 7, 8, 9])

plt.show()
```

xticks() 和 yticks() 需要传入一个列表作为参数。

![xticks() 和 yticks() 需要传入一个列表作为参数](https://img.jikehou.cn/img/20171107_8.png)

该方法默认是将列表的值来设置刻度标签，如果你想重新设置刻度标签，则需要传入两个列表参数给 xticks() 和 yticks() 。第一个列表的值代表刻度，第二个列表的值代表刻度所显示的标签。
```python
...
# 设置横轴精准刻度
plt.xticks([-1, 0, 1, 2, 3, 4, 5, 6],
           ["-1m", "0m", "1m", "2m", "3m", "4m", "5m", "6m"])
# 设置纵轴精准刻度
plt.yticks([-2, 0, 2, 4, 6, 8, 10],
           ["-2m", "0m", "2m", "4m", "6m", "8m", "10m"])
plt.show()
```

![重新设置刻度标签](https://img.jikehou.cn/img/20171107_9.png)

### 2-5.添加图例
如果需要在图的左上角添加一个图例。我们只需要在 plot() 函数里以「键 - 值」的形式增加一个参数。首先我们需要在绘制曲线的时候，增加一个 label 参数，然后再调用 plt.legend() 绘制出一个图例。plt.legend() 需要传入一个位置值。loc 的值可选如下：

| 值      |    说明 | 
| :--------: | :--------:| 
| best       | 自动选择最佳位置，默认是左上 |  
| upper right |   右上 |  
| upper left  |   左上 | 
| lower right |   右下 | 
| lower left  |   左下 | 
| right |   右边，默认是右上。如果因图形挡住右上，会自动往下选择空白地方绘制 | 
| center right |   垂直居中且靠右 | 
| center left  |   垂直居中且靠左 | 
| lower center |   垂直居中且靠底部 | 
| upper center |   垂直居中且靠顶部 | 
| center  |   居中  | 

```python
...
# 绘制颜色为蓝色、宽度为 1 像素的连续曲线 y1
plt.plot(x, y1, color="blue", linewidth=1.0, linestyle="-", label="y1")
# 绘制颜色为紫色、宽度为 2 像素的不连续曲线 y2
plt.plot(x, y2, color="#800080", linewidth=2.0, linestyle="--", label="y2")
plt.legend(loc="upper left")
...
```

![图例](https://img.jikehou.cn/img/20171107_10.png)

### 2-6.注释特殊点位
有时某些数据点非常关键，需要突显出来。我们需要将改点绘制出来，即绘制散点图，再对其做注释。

实现上述需求，我们要用到`scatter()`和`annotate() `函数。`scatter()` 是用于绘制散图，这里我们只是用其来绘制单个点。

scatter() 用法，后续文章会详细对其用法做说明。`annotate() `则是添加标注 。

`scatter()` 函数必须传入两个参数 x 和 y。值得注意得是，它们的数据类型是列表。

x 代表要标注点的横轴位置，y 代表要标注点的横轴位置。x 和 y 列表中下标相同的数据是对应的。

例如 x 为 [3, 4]，y 为 [6, 8]，这表示会绘制点（3，6），（4， 8）。因此，x 和 y 长度要一样。

`annotate`函数同样也有两个必传参数，一个是标注内容，另一个是 xy。标注内容是一个字符串。xy 表示要在哪个位置（点）显示标注内容。xy 位置地选定。

一般是在`scatter()` 绘制点附近，但不建议重合，这样会影响美观。

```python
...
# 绘制颜色为蓝色、宽度为 1 像素的连续曲线 y1
plt.plot(x, y1, color="blue", linewidth=1.0, linestyle="-", label="y1")
# 绘制散点(3, 6)
plt.scatter([3], [6], s=30, color="blue")      # s 为点的 size
# 对(3, 6)做标注
plt.annotate("(3, 6)",
             xy=(3.3, 5.5),       # 在(3.3, 5.5)上做标注
             fontsize=16,         # 设置字体大小为 16
             xycoords='data')  # xycoords='data' 是说基于数据的值来选位置

# 绘制颜色为紫色、宽度为 2 像素的不连续曲线 y2
plt.plot(x, y2, color="#800080", linewidth=2.0, linestyle="--", label="y2")
# 绘制散点(3, 0)
plt.scatter([3], [0], s=50, color="#800080")
# 对(3, 0)做标注
plt.annotate("(3, 0)",
             xy=(3.3, 0),            # 在(3.3, 0)上做标注
             fontsize=16,          # 设置字体大小为 16
             xycoords='data')    # xycoords='data' 是说基于数据的值来选位置
```

![运行结果](https://img.jikehou.cn/img/20171107_11.png)

点已经被标注出来了，如果你还想给点添加注释。这需要使用`text()`函数。text(x，y，s) 作用是在点(x，y) 上添加文本 s。matplotlib 目前好像对中午支持不是很友好， 中文均显示为乱码。

```python
···
# 绘制散点(3, 0)
plt.scatter([3], [0], s=50, color="#800080")
# 对(3, 0)做标注
plt.annotate("(3, 0)", xy=(3.3, 0))
plt.text(4, -0.5, "this point very important",
         fontdict={'size': 12, 'color': 'green'})  # fontdict设置文本字体
```

![运行结果](https://img.jikehou.cn/img/20171107_12.png)

到此为止，我们基本上完成了绘制直线所有工作。Matplotlib 能绘制种类繁多且绘图功能强大，所以我接下来的文章将单独对每种类型图做分享讲解。


