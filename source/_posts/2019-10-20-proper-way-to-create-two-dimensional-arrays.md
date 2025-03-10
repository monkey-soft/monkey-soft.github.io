---
layout: post
title:  "Python 创建二维数组的正确姿势"
date:   2019-10-20 23:48:31
urlname: 109
cover: https://img.jikehou.cn/cover/2019-10-20.jpg
categories: [Python编程]
tags: [热门, Python]
keywords: [Python, 数组]
---
List （列表）是 Python 中最基本的数据结构。

在用法上，它有点类似数组，因为每个列表都有一个下标，下标从 0 开始。

因此，我们可以使用 list[1] 来获取下标对应的值。

如果我们深入下列表的底层原理，会发现列表是基于 PyListObject 实现的。

PyListObject 是一个变长对象，所以列表的长度是随着元素多少动态改变的。

同时它还支持插入和删除等操作，所以它还是一个可变对象。

可以简单理解为，Python 的列表是长度可变的数组。

一般而言，我们用于列表创建都是一维数组。那么问题来，我们如果创建多维数组呢？
<!-- more -->
## 1.列表能创建多维数组？

列表是支持操作符，如果一个列表与 ‘ * ’ 号结合使用，能达到重复列表的效果。比如

```python
list_one = [0]
list_two = [0] * 3
print(list_one)
print(list_two)

>>> 运行结果：

[0]
[0, 0, 0]
```

那么利用这个重复特性，我们是否可以来创建一个二维数组呢？于是乎，我进行一顿猛操作，结果就被我折腾出来了。
```python
list_one = [0]
list_two = [[0] * 3] * 3
print(list_one)
print(list_two)

>>> 运行结果：

[[0, 0, 0], [0, 0, 0], [0, 0, 0]]
```

看起来很完美的操作，但是如果进行一些列表更新操作，问题就显露出来了。

比如我对 list_two 的更换中间位置的值，即对 `list_two[1][1]` 进行更换值。

```python
list_two = [[0] * 3] * 3
print(list_two)

list_two[1][1] = 2
print(list_two)
```

不难发现，运行结果有点不对劲，列表中有三个位置的值也改变了。

```python
[[0, 0, 0], [0, 0, 0], [0, 0, 0]]
[[0, 2, 0], [0, 2, 0], [0, 2, 0]]
```
为什么会出现在这种情况呢？原因是浅拷贝，我们以这种方式创建的列表，list_two 里面的三个列表的内存是指向同一块，不管我们修改哪个列表，其他两个列表也会跟着改变。

如果要使用列表创建一个二维数组，可以使用生成器来辅助实现。

```python
list_three = [[0 for i in range(3)] for j in range(3)]
print(list_three)
list_three[1][1] = 3
print(list_three)
```

我们对 list_three 进行更新操作，这次就能正常更新了。

```python
[[0, 0, 0], [0, 0, 0], [0, 0, 0]]
[[0, 0, 0], [0, 3, 0], [0, 0, 0]]
```

除了以上的方式，还有一种更加简洁方便的方式，就是使用 NumPy 模块。

## 相比 List，NumPy 数组的优势

NumPy 全称为 Numerical Python，是 Python 的一个以矩阵为主的用于科学计算的基础软件包。N

umPy 和 Pandas、Matpotlib 经常结合一起使用，所以被人们合称为数据分析三剑客。

Numpy 中有功能强大的 ndarray 对象，能创建 N 维的数组，另外还提供很多通用函数，支持对数组的元素进行操作、支持对数组进行算法运算以及提供常用的统计函数。

相比 List 对象，NumPy 数组有以下优势：
1.这是因为列表 list 的元素在系统内存中是分散存储的，而 NumPy 数组存储在一个均匀连续的内存块中。
这样数组计算遍历所有元素，不像列表 list 还需要对内存地址进行查找，从而节省了计算资源。

2.Numpy数组能够运用向量化运算来处理整个数组，速度较快；而 Python 的列表则通常需要借助循环语句遍历列表，运行效率相对来说要差。

3.NumPy 中的矩阵计算可以采用多线程的方式，充分利用多核 CPU 计算资源，大大提升了计算效率。

4.Numpy 使用了优化过的 C API，运算速度较快。

## 2.创建数组

前面说到 NumPy 的主要对面是 ndarray 对象，它其实是一系列同类型数据的集合。

因为 ndarray 支持创建多维数组，所以就有两个行和列的概念。

### 创建 ndarray 的第一种方式是利用 array 方式。

```python
import numpy as np
# 创建一维数组
nd_one = np.array([1, 2, 3])
# 创建二维数组
nd_two = np.array([[1, 2, 3], [4, 5, 6]])

print(nd_one)
print(nd_two)
print('nd_two.shape =', nd_one.shape)
print('nd_two.shape =', nd_two.shape)

>>> 运行结果：

[1 2 3]
[[1 2 3]
 [4 5 6]]
nd_two.shape = (3,)
nd_two.shape = (2, 3)
```
其中 shape 是数组的一个属性，表示获取数组大小(有多少行，有多少列)，如果是一维数组，则只显示（行，）。代码中打印出 nd_two 的形状，输出为（2，3），表示数组中有 2 行 3 列。

第二种办法则使用 Numpy 的内置函数

### 使用arange 或 linspace 创建连续数组。

```python
import numpy as np
# arange() 类似Python内置函数的 range()
# arange(初始值, 终值, 步长) 不包含终值
x0 = np.arange(1, 11, 2)
print(x0)

# 创建一个 5x3 的数组
x1 = np.arange(15).reshape((5, 3))
print(x1)

# linspace()线性等分向量
# linspace(初始值, 终值, 元素个数) 包含终值
x2 = np.linspace(1, 11, 6)
print(x2)

>>> 运行结果：

[1 3 5 7 9]

[[ 0  1  2]
 [ 3  4  5]
 [ 6  7  8]
 [ 9 10 11]
 [12 13 14]]

[  1.   3.   5.   7.   9.  11.]
```
虽然 `np.arange` 和 `np.linspace` 起到的作用是一样的，都是创建等差数组，但是创建的方式是不同的。

### 使用 zeros()，ones()，full() 创建数组

```python
import numpy as np
# 创建一个 3x4 的数组且所有值全为 0
x3 = np.zeros((3, 4), dtype=int)
print(x3)
# 创建一个 3x4 的数组且所有元素值全为 1
x4 = np.ones((3, 4), dtype=int)
print(x4)
# 创建一个 3x4 的数组，然后将所有元素的值填充为 2
x5 = np.full((3, 4), 2, dtype=int)
print(x5)

>>> 运行结果：

[[0 0 0 0]
 [0 0 0 0]
 [0 0 0 0]]

[[1 1 1 1]
 [1 1 1 1]
 [1 1 1 1]]

[[2 2 2 2]
 [2 2 2 2]
 [2 2 2 2]]
```

### 使用 eye() 创建单位矩阵

eye() 创建的数组特点是行数和列数都是一样。

因为它创建出来的是单位矩阵，单位矩阵是正形矩阵，对角线的值均为 1，其他位置的值为 0。

```python
import numpy as np
# 创建 3x3 的单位矩阵
x6 = np.eye(3, dtype=int)
print(x6)

>>> 运行结果：

[[1 0 0]
 [0 1 0]
 [0 0 1]]
```

### 使用 diag() 创建对角矩阵
diag() 是创建一个 NxN 的对角矩阵，对角矩阵是对角线上的主对角线之外的元素皆为 0 的矩阵。

```python
import numpy as np
x7 = np.diag([1, 2, 3])
print(x7)

>>> 运行结果：

[[1 0 0]
 [0 2 0]
 [0 0 3]]
```

### 使用 random 创建随机数组
numpy 中的 random 中有很多内置函数，能简单介绍其中的几种。
```python
import numpy as np
# 创建 2x2 数组且所有值是随机填充
x9 = np.random.random((2, 2))
print(x9)

# 创建一个值在 [0, 10) 区间的 3x3 的随机整数
x10 = np.random.randint(0, 10, (3, 3))
print(x10)

>>> 运行结果：

[[ 0.77233522  0.41516417]
 [ 0.22350126  0.31611254]]

[[0 6 5]
 [7 6 4]
 [5 5 9]]
```