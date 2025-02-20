---
layout: post
title:  "Python 三种遍历目录的方法，轻松帮你找出隐藏文件"
date:   2018-11-05 10:54:01
urlname: 85
cover: https://img.jikehou.cn/cover/2018-11-05.jpg
categories: [小白入门Python]
tags: [Python]
keywords: [Python, 文件遍历]
---
无论在 Windows 系统中还是 Linux 系统中，都存在着隐藏文件以及隐藏文件夹。

隐藏文件夹一般是系统关键性目录，例如 Windows 系统中的 C 盘中的 `Boot 文件夹`、`Windows 文件夹`等。

在 Linux 系统中，一些系统配置文件或软件配置文件会被隐藏起来，如：系统环境变量配置文件 `.profile`。

有些时候，我们必须通过文件路径来遍历整个目录，然后找到隐藏文件。

本文的主要内容是给大家分享 Python 三种遍历文件的方法。
<!-- more -->
## 1.简单暴力法-递归

假设在 E 盘中，有个名为“Python”的文件夹；

该文件夹中也有两个文件夹，分别是“A”和“B”；

另外，在“A”文件夹中还有一个 “results.txt” 的文本文件。

因此，“Python”文件夹的文件结构如下：

```Python
Python
|--A
|  |--results.txt
|--B
```

我们可以从上述看出，一个文件夹其实是一个树型的数据结构。

遍历树的最简单、最暴力的办法就是递归。

因此，遍历“Python”的文件夹的代码可以这么写。

```Python
# -*- coding: UTF-8 -*-
import os

# 递归遍历目录
def traversal_files(path):
    for dir in os.listdir(path):
        dir = os.path.join(path, dir)
        print(dir)
        # 判断当前目录是否为文件夹
        if os.path.isdir(dir):
            traversal_files(dir)

if __name__ == '__main__':
    path = '.'
    traversal_files(path)
```

运行脚本程序后，发现“Python”中的文件和文件夹都被打印出来。

![运行脚本程序](https://img.jikehou.cn/img/20181105_1.png)

这种遍历方法能否找出隐藏文件？答案是肯定能。

让我们来验证一番。

首先，我们在“Python”的文件夹中放入一个“config.txt”的隐藏文件。

![config.txt隐藏文件](https://img.jikehou.cn/img/20181105_2.png)

然后再运行程序，结果发现“config.txt”被打印出来。

![结果发现“config.txt”被打印出来](https://img.jikehou.cn/img/20181105_3.png)


这种办法虽然写起来代码简洁，但是在文件夹的子目录层级过深的情况下，效率会比较低。

## 2.优雅的 os.walk()

既然递归太暴力，那么使用 `os.walk()` 会让程序显得优雅。

os.walk() 方法是一个简单易用的文件、目录遍历器，可以帮助我们高效的处理文件、目录方面的事情。

这个方法适用于在 Linix 和 Windows。

os.walk() 一般用法是传入两个参数。第一个参数是 path， 即所要遍历的目录的地址。

它返回的是一个三元组(root, dirs, files)。

* root 所指的是当前正在遍历的这个文件夹的本身的地址
* dirs 是一个 list ，内容是该文件夹中所有的目录的名字(不包括子目录)
* files 同样是 list , 内容是该文件夹中所有的文件(不包括子目录)

第二个参数是**topdown**，它是一个可选参数。当它的值为 True 时，则优先遍历 path 目录，否则优先遍历 top 的子目录(默认为开启)。

因此，使用 **os.walk** 遍历文件夹，找出隐藏文件的代码如下所示。

```Python
# -*- coding: UTF-8 -*-
import os

def traversal_files(path):
   9 for root, dirs, files in os.walk(path, topdown=False):
        for name in files:
            print(os.path.join(root, name))
        for name in dirs:
            print(os.path.join(root, name))

if __name__ == '__main__':
    path = '.'
    traversal_files(path)
```

## 3.高效的 os.scandir()

在 Python 3.5 版本中，新添加了 `os.scandir()`方法，它是一个目录迭代方法。

`os.scandir()` 的运行效率要比 `os.walk` 高。

在 PEP 471 中，Python 官方也推荐我们使用 `os.scandir()` 来遍历目录。

按照前面的例子，遍历“Python”的文件夹中的隐藏文件的代码如下：
```Python
# -*- coding: UTF-8 -*-
import os

dirs = []
files = []

def traversal_files(path):
    for item in os.scandir(path):
        if item.is_dir():
            dirs.append(item.path)
        elif item.is_file():
            files.append(item.path)

    print('dirs:', dirs)
    print('files:', files)

if __name__ == '__main__':
    path = '.'
    traversal_files(path)
```

运行结果，同样也是能找出隐藏文件。

![同样也是能找出隐藏文件](https://img.jikehou.cn/img/20181105_4.png)
