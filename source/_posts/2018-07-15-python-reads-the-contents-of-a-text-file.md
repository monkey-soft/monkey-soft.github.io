---
layout: post
title:  "Python 读取文本文件的内容"
date:   2018-07-15 15:43:50
urlname: 67
cover: https://img.jikehou.cn/cover/2018-07-15.jpg
categories: [小白入门Python]
tags: [Python, 文本操作]
keywords: [Python, 文本操作]
---
数据存储方式有很多种。

如果数据的数据量比较大、数据类型繁多且要求便于搜索，我们一般会选择存储到数据库中。

如果数据内容只是一些的文本信息，我们可以将数据存储到 TXT 、JSON、CSV 等文本文件中。

类似存储小说、日志内容等场景，一般是将内容存储到文本文件中。

数据已经存储到 txt 文件中，那该如何读取了？

本文的主要内容是讲解如何读取文本文件的内容。
<!-- more -->
## 1.打开文件
文本操作可以想象成对水池进行加水和排水。

文本文件就好比一个存储水的水池，数据就类似水。

从文本文件中读取数据好比让水池排水。

在这过程中，我们需要一条“管道”才能从读取到数据。

在 Python 语言中，`open()` 函数就是这样的“管道”。当 `open()` 函数成功打开文件后，我们会得到一个 `file` 对象。

```python
file = open('One Day.txt', 'r')
```

但是操作文件经常会出现各种异常，例如文件不存在，文件不具备可读属性等。

因此，我们需要做异常处理工作。

这里推荐使用 `with` 语句，其内部已经实现异常处理相关的逻辑。

另外还有一个好处，我们还可以不用调用 `close()` 函数来关闭文件。

```python
with open('One Day.txt', 'r') as file:
    pass
```

open() 函数的第二个参数是打开模式。可以是只读`r`，写入`w`，追加`a`、以二进制形式读取`rb`等。

## 2.read()
read() 函数读取数据方式有点暴力。

它是一次性将文件的全部内容读取到内存中。

如果文件太多的话，会把内存给撑爆。

为了保险起见，我们通常每次只读取一小段区间内容，然后反复调用。
```python
# -*- coding:utf-8 -*-
size = 1024
with open('one day.txt', 'r') as file:
    print(file.read(size))
```

## 3.readline()
如果我们需要每次只读取一行内容，则需要用到`readline()` 函数。

这种读取方式虽然效率不高，但是占用内存小，能做到即读即用。
```python
# -*- coding:utf-8 -*-
import time

with open('one day.txt', 'r') as file:
    for line in file:
        # realine() 读取整行内容，包括 "\n" 字符
        print(file.readline().strip())
        time.sleep(1)
```

## 4.readlines()
`realines()` 函数跟`read()`类似，会一次性读取所有内容，然后按行返回一个 list 对象。

这种读取方式速度会比较快。但随着文本的增大，占用内存会越来越多。

一般读取配置文件，可以使用这种方法。
```python
# -*- coding:utf-8 -*-
import time

with open('one day.txt', 'r') as file:
    for line in file.readlines():
        print(line.strip())
        time.sleep(1)
```