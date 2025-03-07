---
layout: post
title:  "彻底理解 Iterable、Iterator、generator"
date:   2017-11-21 15:06:17
urlname: 24
cover: https://img.jikehou.cn/cover/2017-11-21.jpg
categories: [Python编程]
tags: [Python]
keywords: [Python, generator, iterator, iterable]
---
本文介绍猴哥对于Python中的Iterable、Iterator、generator的理解。
<!-- more -->
## 1.Iterable
我们一般称`Iterable`为`可迭代对象`。

Python 中任意的对象，只要它定义了可以返回一个迭代器的`__iter__`方法，或者定义了可以支持下标索引的`__getitem__`方法，那么它就是一个可迭代对象。

我们常用到的集合数据类型都是 Iterable。

例如列表（list）、元组（tuple）、字典（dict）、集合（set）、字符串（str）等。

我定义了一个列表 numlist，打印出该列表的方法。
```python
numlist = [1, 2, 3]
print(numlist)
print(numlist.__iter__)      # 调用__iter__方法
print(numlist.__getitem__)  # 调用__getitem__方法
```

运行结果如下：
![运行结果](https://img.jikehou.cn/img/20171121_1.png)

根据运行结果，我们可知列表就是个可迭代对象。

Python 的`collections `库有个`isinstance()`函数。可以用来判断一个对象是否是 Iterable 对象。
```python
from collections import Iterable  

isinstance({}, Iterable)  
isinstance((), Iterable) 
isinstance(999, Iterable)
```

运行结果为：
![运行结果](https://img.jikehou.cn/img/20171121_2.png)

如果我们每次都要使用这个函数来判断一个对象是否为可迭代对象，这样操作有点麻烦。

有没有快速判定的方法呢？答案是肯定的。

可以直接使用 for 循环进行遍历的对象就是可迭代对象。

除此之外，`generator(生成器)` 和`带 yield 的 generator function` 也是可迭代的对象。


## 2.Iterator
`Iterator`是迭代器的意思。任意对象，只要定义了`next()`（Python 2 版本）或者`__next__()`（Python 3 版本） 方法，那么它就是一个迭代器。

迭代器中还有另一个函数`__iter__()`，它和 __next__() 方法形成迭代器协议。

- __iter__()
返回主要是返回迭代器对象本身，即`return self`。如果你自己定义个迭代器，实现该函数就能使用`for  ...  in ...`语句遍历了。

- __next__() 
获取容器中的下一个元素，当没有可访问元素后，就抛出`StopIteration`异常。

遍历迭代器有两个方式。一种是使用 __next__() 函数；另一种则是使用 for each 循环，本质上就是通过不断调用 __next__() 函数实现的。
```python
from collections import Iterator

numlist = [1, 2, 3]

# 将数组转化为迭代器
ite1 = iter(numlist)
print(ite1)

for i in ite1:
    print(i)

print("=========")

ite2 = iter(numlist)
while True:
    try:
        num = ite2.__next__()
        print(num)
    except StopIteration:
        break
```

值得注意的是一个 Iterator 只能遍历一次。

## 3.generator
generator 翻译成中文是生成器。生成器也是一种特殊迭代器。它其实是生成器函数返回生成器的迭代，“生成器的迭代器”这个术语通常被称作”生成器”。

yield 是生成器实现`__next__() `方法的关键。

它作为生成器执行的暂停恢复点，可以对 yield 表达式进行赋值，也可以将 yield 表达式的值返回。任何包含 yield 语句的函数被称为生成器。

yield是一个语法糖，内部实现支持了迭代器协议，同时yield内部是一个状态机，维护着挂起和继续的状态。

个人认为，生成器算是 Python 非常棒的特性。它的出现能帮助大大节省些内存空间。

假如我们要生成从 1 到 10 这 10 个数字，采用列表的方式定义，会占用 10 个地址空间。采用生成器，只会占用一个地址空间。因为生成器并没有把所有的值存在内存中，而是在运行时生成值。所以生成器只能访问一次。

创建一个从包含 1 到 10 的生成器的例子。
```python
gen = (i for i in range(10))
print(gen)
for i in gen:
    print(i)
```

运行结果如下：
![运行结果](https://img.jikehou.cn/img/20171121_3.png)

带有 yield 关键字 的例子。重点关注运行结果，这能让你对 yield 有更深的认识。
```python
def testYield(n):
    for i in range(n):
        print("当前值: ", i)
        yield doubeNumber(i)
        print("第 ", i, " 次运行")
    print("testYield 运行结束")

def doubeNumber(i):
    return i*2
    
if __name__ == '__main__':
    for i in testYield(3):
        print(i, "===", i)
```

运行结果如下：
![运行结果](https://img.jikehou.cn/img/20171121_4.png)