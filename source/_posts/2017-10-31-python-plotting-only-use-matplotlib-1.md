---
layout: post
title:  "Python 绘图,我只用 Matplotlib(一)"
date:   2017-10-31 11:04:54
urlname: 22
cover: https://img.jikehou.cn/cover/2017-10-31.jpg
categories: [数据分析]
tags: [热门, Python, 数据分析, Matplotlib]
keywords: [Python, 数据分析, Matplotlib, 数据可视化]
---
> 当我们的爬虫程序已经完成使命，帮我们抓取大量的数据。你内心也许会空落落的。或许你会疑惑，自己抓取这些数据有啥用？如果要拿去分析，那要怎么分析呢？

说到数据分析，Python 完全能够胜任这方面的工作。Python 究竟如何在数据分析领域做到游刃有余？

因为它有“四板斧”，分别是Matplotlib、NumPy、SciPy/Pandas。

Matplotlib 是画图工具，NumPy 是矩阵运算库，SciPy 是数学运算工具，Pandas 是数据处理的工具。
<!-- more -->

## 1.为什么选择 Matplotlib？
Python 有很多强大的画图库，为什么我偏偏独爱 Maplotlib？我先买个关子，先来看看还有哪些库。

### 1-1.Seaborn

Seaborn 是一个基于 Matplotlib 的高级可视化效果库， 偏向于统计作图。

因此，针对的点主要是数据挖掘和机器学习中的变量特征选取。

相比 Matplotlib ，它语法相对简化些，绘制出来的图不需要花很多功夫去修饰。

但是它绘图方式比较局限，不过灵活。

### 1-2.Bokeh
Bokeh 是基于 javascript 来实现交互可视化库，它可以在WEB浏览器中实现美观的视觉效果。

但是它也有明显的缺点。其一是版本时常更新，最重要的是有时语法还不向下兼容。

这对于我们来说是噩梦。其二是语法晦涩，与 matplotlib做比较，可以说是有过之而无不及。

### 1-3.ggplot
ggplot 是 yhat 大神基于 R 语言的 ggplot2 制作的 python 版本库。 如果你使用 R 语言的话，ggplot2 可以算是必不可少的工具。所以，很多人都推荐使用该库。不过可惜的是，yhat 大神已经停止维护该库了。

### 1-4.Plotly
Plotly 也是一个做可视化交互的库。它不仅支持  Python 还支持 R 语言。Plotly 的优点是能提供 WEB 在线交互，配色也真心好看。如果你是一名数据分析师，Plotly 强大的交互功能能助你一臂之力完成展示。

### 1-5.Mapbox
Mapbox 使用处理地理数据引擎更强的可视化工具库。如果你需要绘制地理图，那么它值得你信赖。

总之， Python 绘图库众多，各有特点。但是 Maplotlib 是最基础的 Python 可视化库。如果你将学习 Python 数据可视化。那么 Maplotlib 是非学不可，然后再学习其他库做纵横向的拓展。

## 2.Matplotlib 能绘制什么图？
Matiplotlib 非常强大，所以最基本的图表自然不在话下。
例如说：
### 2-1.直线图
![直线图](https://img.jikehou.cn/img/20171031_1.png)

### 2-2.曲线图
![曲线图](https://img.jikehou.cn/img/20171031_2.png)

### 2-3.柱状图
![柱状图](https://img.jikehou.cn/img/20171031_3.png)

### 2-4.直方图
![直方图](https://img.jikehou.cn/img/20171031_4.png)

### 2-5.饼图
![饼图](https://img.jikehou.cn/img/20171031_5.png)

### 2-6.散点图
![散点图](https://img.jikehou.cn/img/20171031_6.png)

只能绘制这些最基础的图？显示是不可能的，还能绘制些高级点的图
例如：
### 2-7.高级点的柱状图
![高级点的柱状图](https://img.jikehou.cn/img/20171031_7.png)

### 2-8.等高线图
![等高线图](https://img.jikehou.cn/img/20171031_8.png)

### 2-9.类表格图形
![类表格图形](https://img.jikehou.cn/img/20171031_9.png)

不仅仅只有这些，还能绘制 3D 图形。
例如
### 2-10.三维柱状图
![三维柱状图](https://img.jikehou.cn/img/20171031_10.png)

### 2-11.3D 曲面图
![3D 曲面图](https://img.jikehou.cn/img/20171031_11.png)

因此，Matplotlib 绘制的图种类能够满足我们做数据分析了。


## 3.安装 Matplotlib
看到这里，你是否惊叹不已，很很迫不及待地想学习 Matplotlib。

而工欲善其事，必先利其器。

我们先来学习如何安装 Matplotlib。其实也是很简单，我们借助 pip 工具来安装。

在终端执行以下命令来安装 Matplotlib
```python
pip install Matplotlib 
# 如果出现因下载失败导致安装不上的情况，可以先启动 ss 再执行安装命令
# 或者在终端中使用代理
pip --proxy http://代理ip:端口 install Matplotlib 
```