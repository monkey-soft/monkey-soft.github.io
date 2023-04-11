---
layout: post
title:  "os.path 模块用法详解"
date:   2018-07-11 22:08:16
urlname: 66
cover: https://img.jikehou.cn/cover/2018-07-11.jpg
categories: [小白入门Python]
tags: [Python]
keywords: [Python, os.path, 标准库]
---
总所周知，Windows 操作系统和 Linux 系统存在很多不兼容的地方。

文件路径就是一个明显的例子。

在 Linux 中，路径的分割采用正斜杠 `/`，比如 `/home/monkey`；

而在 Windows 中，路径分隔采用反斜杠 `\`，比如 `C:\Windows\System`。

我们在编码过程中，往往需要和文件或文件夹打交道。

如果程序中涉及到到路径操作，我们最好使用 Python 标准库中的 os.path 模块来实现。

这样能避免出现程序无法多平台运行的问题。

`os.path` 全称是`Common pathname manipulations`, 译为通用路径名操作。

其作用是提供操作各种处理文件名以及路径名的函数。

因此，本文的内容是讲述`os.path`模块中几个常见函数的用法。
<!-- more -->
## 1.获取文件(夹)绝对路径
`abspath(path)` 函数返回的是 path 经过规范化的绝对路径。

假设在 D 盘中有 Downloads 的文件夹，该文件夹中有叫 `cat.jpg` 的图片。

我们要获取这张图片的经过转椅的绝对路径，可以在 Downloads 文件夹中打开 DOS 窗口，然后进入 Python 环境。

```python
# 先引用 os 标准库
import os
os.path.abspath('cat.jpg')
>> 'D:\\Downloads\\cat.jpg'
```

## 2.从路径中切割出文件名
`split(path)` 函数的作用是将传入的 path 分割成目录和文件名，然后以元组形式返回。
```python
import os
# Downloads 文件夹下有个 cat.jpg 的图片
os.path.split('D:\\Downloads\\cat.jpg')
>> ('D:\\Downloads', 'cat.jpg')

# Downloads 文件夹下有个 animal 的文件夹
os.path.split('D:\\Downloads\\animal')
>> ('D:\\Downloads', 'animal')

# 如果 path 是以两个反斜杠 \\ 结尾，则返回空
os.path.split('D:\\Downloads\\')
>> ('D:\\Downloads', '')
```

## 3.将多个文件路径合并
多个路径组合合并需要用到函数 `join(path1, path2, ...)`。

join 函数合并路径有个特点：在所有 path 参数中，只会从第一个绝对路径的参数开始合并。

这也意味着第一个绝对路径之前的参数将被忽略。
```python
import os
os.path.join('Downloads', 'D:\\', 'animal')
>> 'D:\\animal'

# 第一个参数是绝对路径，则按顺序合并
os.path.join('D:\\', 'Downloads', 'animal')
>> 'D:\\Downloads\\animal'
```

## 4.判断路径是否存在
`exists(path)` 函数是判断一个文件或者文件夹的路径是否存在。

如果 path 存在，返回 True；如果 path 不存在，则返回 False；
```python
import os

# D 盘确实有个 Downloads 的文件夹
os.path.exists('D:\\Downloads\\')
>> True

# Downloads 还有个 animal 的文件夹
os.path.exists('D:\\Downloads\\animal\\')
>> True

# D 盘没有名为 cat 的文件夹
os.path.exists('D:\\cat\\')
>> False
```

## 5.判断路径是否是一个存在的文件
判断 path 是否是一个存在文件，函数 `isfile(path)` 就能轻松搞定。

如果 path 是一个存在的文件，返回True。否则返回 False。
```python
import os

os.path.isfile('D:\\Downloads\\cat.jpg')
>> True

os.path.isfile('D:\\Downloads\\animal')
>> False
```

## 6.将路径规范化
`normcase(path)` 的作用是解决操作系统间路径分割符的兼容性。

在 Linux 和 Mac 平台上，该函数会原样返回path，在 Windows 平台上会将路径中所有字符转换为小写，并将所有斜杠转换为反斜杠。
```python
import 

# Linux 系统下
os.path.normcase('/home/monkey')
>> '/home/monkey'

# Window 系统下
os.path.normcase('D:/download\\animal')
>> 'd:\\download\\animal'
```

除了上述的常用函数之外，os.path 还有其他函数。

感兴趣的同学浏览 [Python 官网文档](https://docs.python.org/3/library/os.path.html)。
