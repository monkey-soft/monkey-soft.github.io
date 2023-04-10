---
layout: post
title:  "几个Python编程小技巧"
date:   2018-01-23 15:29:44
urlname: 33
cover: https://img.jikehou.cn/cover/2018-01-23.jpg
categories: [小白入门Python]
tags: [Python]
keywords: [Python, python编程技巧]
---
本文，猴哥分享几个 Python 编程的小技巧。
<!-- more -->
## 1.编码问题
我们在爬取网站是，会经常抓取网页文本，但是打印文本会出现是一堆乱码。

这是为什么呢？原因是 Python 中字符对象分为两种，一种是 Unicode 对象，另一种是 str 对象。

字符在 Python 中又以 Unicode 对象为基础，所以我们定义的字符串在内存中以 Unicode 编码的形式存储。

另外，str 对象又可以有多种编码形式，如 UTF-8、GBK-2312 等。虽然不同编码的 str 对象能被解码成 unicode 对象，但是不同编码的 str 对象直接不能直接转换。

因此，如果字符串编码是 GB2312，将其存储到 list 中，再打印出来看到乱码是 Unicode 编码。

解决这个问题其实很简单，Unicode 作为中间编码。我们只要将一种字符编码（如 GB2312）的字符串解码为 Unicode 编码，再编码为另外一种字符编码（如 UTF-8）。
```python
# 数据库以 UTF-8 形式保存字符串，而获取到的字符串是 GB2312
str = getFromNetWork()      # 获取网络字符串，字符编码为 GB2312
str.encode('GB2312').decode('UTF-8') 
print(str)
```


## 2.值交换
在 C 或者 Java 中，要将两个变量的值进行交换。我们需要一个临时变量来存储其中一个值。
```Java
int a=10, b=20,temp;
temp = a;
a = b;
b = temp;
```

但是在 Python中，有个简单的办法能直接一步到位。
```python
a = 10
b = 20
print('a = ', a, ' b = ', b)
b, a = a, b
print('a = ', a, ' b = ', b)

>>  a =  10  b =  20
>>  a =  20  b =  10
```


## 3.单例模式
Python 不仅是面向过程的编程语言，而且是面向对象的编程语言。

在 Python 中，一个类被初始化，那么 `__new__()` 函数一定会先被调用，然后再调用`__init__()`。

我们可以采用 hasattr() 函数来判断对象是否包含对应的属性，也就是判断类是否被初始化。
```python
class Singleton(object):
    def __new__(cls):
        # 关键在于这，每一次实例化的时候，我们都只会返回这同一个instance对象
        if not hasattr(cls, 'instance'):
            cls.instance = super(Singleton, cls).__new__(cls)
        return cls.instance

obj1 = Singleton()
obj2 = Singleton()
obj1.name = 'GeeMonkey'
print(obj1.name, obj2.name)
print(obj1 is obj2)

>>  GeeMonkey GeeMonkey
>>  True
```