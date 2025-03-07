---
layout: post
title:  "Python 中各种时间类型的转换"
date:   2018-01-17 11:18:23
urlname: 32
cover: https://img.jikehou.cn/cover/2018-01-17.jpg
categories: [Python编程]
tags: [Python]
keywords: [Python, 时间转换]
---
我们编码过程中经常需要获取当前时间。当然， 这也离不开对时间类型进行转换运算。

本文主要讲解 Python 各种时间类型之间的转换。
<!-- more -->
## 1.处理时间的库
Python 标准库中有两个处理时间的库。其中一个名为 `datetime`，另一个是`time`。

在 Python 官网文档中，datetime 是被定义为数据类型(Data Types)。

由此可见，datetime  是主要提供处理日期和时间的数据类型的模块。

它其中有几个常用的类型，例如：datetime.datetime、datetime.time、datetime.date 等，其中最主要的类是`datetime.datetime`。

因为它携带了 datetime.time 和 datetime.date 这两个所带的信息，能够比较齐全地输出，即能一次性就输出年、月、日、时、分、秒等日期和时间信息。

time 模块是归属于通用操作系统服务（Generic Operating System Services）类目中。time 模块主要提供各种时间转换的函数。

它服务于系统层次，Python 又是跨平台的，所以有些 API 只能在某些操作系统上使用。

## 2.时间类型对象
在进行时间转换之前，我们要确认下时间对象是属于哪种数据类型。只有做到对症下药，根治病因。
在 Python 中，涉及时间对象有 4 种：
1）datetime
2）timestamp
3）time tuple
4）string

### 2-1.datetime 
datetime 对象属于 datetime 模块。它的构造方式是 `datetime(year, month, day, hour=0, minute=0, second=0, microsecond=0, tzinfo=None)`。

我们了解下它的构造方法即可，一般很少直接使用它的构造方法。我们一般使用它的 `now()` 函数来获取本地当前日期和时间。
```python
import datetime

now = datetime.datetime.now()
print(now)
print(type(now))

>>  2018-01-17 16:49:24.314323
>>  <class 'datetime.datetime'>
```

### 2-2.string
在某些场景，我们可能需要使用到字符串类型的时间。我们在 now() 函数的基础上再调用 `strftime()` 函数即可。

strftime() 返回的是一个表示日期和时间的字符串。最后显示结果由指定样式的参数决定。
```python
import datetime
now = datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
print(now)
print(type(now))

>>  2018-01-17 17:30:45
>>  <class 'str'>
```

### 2-3.timestamp 
timestamp 指的是 Unix 时间戳。

它是格林威治时间 1970 年 01 月 01 日 00 时 00 分 00 秒(北京时间 1970 年 01 月 01 日 08 时 00 分 00 秒)起至现在的总秒数。

我们使用 time 对象中的 `time()` 函数能获取到时间戳 。
```python
import time

now = time.time()
print(now)
print(type(now))

>>  1516179935.351417
>>  <class 'float'>
```

### 2-4.time tuple
time tuple 表示时间元组。time tuple 是 time.struct_time 对象类型。

获取本地当前时间，一般是使用 time 中的 `localtime()` 函数。

localtime() 返回的是将时间戳经过格式化的本地时间。
```python
import time
now = time.localtime()
print(now)
print(type(now))

>>  time.struct_time(tm_year=2018, tm_mon=1, tm_mday=17, tm_hour=17, tm_min=20, tm_sec=34, tm_wday=2, tm_yday=17, tm_isdst=0)
>>  <class 'time.struct_time'>
```

## 3.时间类型转换
上述四种时间类型是如何转换，我本打算以代码的形式加以说明。

当后来我看到互联网上已经有前辈整理的关系转换图，我意识到自己这种方式不够简洁明了。

所以，我直接献上前辈的宝图。
![时间类型转换图](https://img.jikehou.cn/img/20180117_1.jpg)

