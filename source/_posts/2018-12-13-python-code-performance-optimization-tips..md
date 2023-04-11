---
layout: post
title:  "Python 代码性能优化技巧"
date:   2018-12-13 20:20:28
urlname: 91
cover: https://img.jikehou.cn/cover/2018-12-13.jpg
categories: [小白入门Python]
tags: [Python]
keywords: [Python, 性能优化]
---
众所周知，程序的性能好坏影响着用户体验。

所以性能是留住用户很重要的一环。

Python 语言虽然能做很多事情，但是有一个不足之处，那就是执行效率和性能不够理想。

因此，更有必要进行一定的代码优化来提高 Python 程序的执行效率。

本文章主要是输出自己在 Python 程序优化的经验。
<!-- more -->
## 1.尽量使用内置函数
Python 的标准库中有很多内置函数，它们的运行效率都很高。

因为很多标准库是使用 C 语言编写的。

Pyhton 的内置函数有：

![Pyhton 的内置函数](https://img.jikehou.cn/img/127_1.png)

原图比较大，排版原因可能导致字体看不清。

在公众号后台中，回复【内置函数】即可获取高清图片。

## 2.拼接字符串
运算符 "`+`" 不仅能用于加法运算，还能做字符串连接。

但是这种效率不是很高。

在 Python 中，字符串变量在内存中是不可变的。

如果使用 "`+`" 拼接字符串，内存会先创建一个新字符串，然后将两个旧字符串拼接，再复制到新字符串。

推荐使用以下方法：

### 2-1.使用 "%" 运算符连接

这种方式有点像 C 语言中 printf 函数的功能，使用 "`%s`" 来表示字符串类型参数，再用 "`%`" 连接一个字符串和一组变量。
```Python
fir = 'hello'
sec = 'monkey'
result = '%s, %s' % (fir, sec)
print(result)
```

### 2-2.使用 format() 格式化连接

这种格式化字符串函数是 Python 特有的，属于高级用法。

因为它威力强大，不仅支持多种参数类型，还支持对数字格式化。
```Python
fir = 'hello'
sec = 'monkey'
result = '{}, {}'.format(fir, sec)
print(result)
```

上述代码使用隐式的位置参数，format() 还能显式指定参数所对应变量的位置。

```Python
fir = 'hello'
sec = 'monkey'
result = '{1}, {0}'.format(fir, sec)
print(result)
```

### 2-3.使用 join() 方式

这种算是技巧性办法。join() 方法通常是用于连接列表或元组中的元素。
```Python
list = ['1', '2', '3']
result = '+'.join(list)
print(result)
```

## 3.使用 generator
generator 翻译成中文是生成器。生成器也是一种特殊迭代器。

它其实是生成器函数返回生成器的迭代。

生成器算是 Python 非常棒的特性。

它的出现能帮助大大节省些内存空间。

假如我们要生成从 1 到 10 这 10 个数字，采用列表的方式定义，会占用 10 个地址空间。

采用生成器，只会占用一个地址空间。

因为生成器并没有把所有的值存在内存中，而是在运行时生成值。

所以生成器只能访问一次。

```Python
# 创建一个从包含 1 到 10 的生成器
gen = (i for i in range(10))
print(gen)
for i in gen:
    print(i)
```

## 4.死循环
虽然使用`While True`和`while 1`都能实现死循环，但是`while 1`是单步运算，所以效率会高一点。
```Python
# 推荐
while 1:
    # todo list
    
while True:
    # todo list
```

## 5.巧用多重赋值
交换将两个变量的值，我们会立马想到应用一个第三方变量的方法。

```Python
# 将 a 和 b 两个值互换
temp = a
a = b
b = temp
```

Python 素有优雅的名声，所以有一个更加优雅又快速的方法，那就是多重赋值。
```Python
# 将 a 和 b 两个值互换
a, b = b, a
```

## 6.列表的插入与排序
Python 标准库中有个 `bisect` 模块是内置模块，它实现了一个算法用于插入元素到有序列表。

在一些情况下，这比反复排序列表或构造一个大的列表再排序的效率更高。

```Python
import bisect

L = [1,3,3,6,8,12,15]
x = 3

x_insert_point = bisect.bisect_left(L, x)
# 在 L 中查找 x，x 存在时返回 x 左侧的位置，x 不存在返回应该插入的位置
# 这是3存在于列表中，返回左侧位置１
print(x_insert_point)

x_insert_point = bisect.bisect_right(L, x)
# 在 L 中查找 x，x 存在时返回x右侧的位置，x 不存在返回应该插入的位置
# 这是3存在于列表中，返回右侧位置３
print(x_insert_point)

x_insort_left = bisect.insort_left(L, x)
#将 x 插入到列表 L 中，x 存在时插入在左侧
print(L)

x_insort_rigth = bisect.insort_right(L, x)
#将 x 插入到列表L中，x 存在时插入在右侧
print(L)
```

## 7.尽量使用局部变量
Python 检索局部变量比检索全局变量快。因此, 尽量少用 "`global`" 关键字。



