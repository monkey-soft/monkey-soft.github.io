---
layout: post
title:  "Django 学习笔记之模型高级用法(下)"
date:   2018-04-27 17:17:56
urlname: 50
cover: https://img.jikehou.cn/cover/2018-04-27.jpg
categories: [Python编程]
tags: [Python, Web开发]
keywords: [Python, web, Django]
---
接着上篇文章内容，本文分享自己对模型一些用法的总结。
<!-- more -->

## 1.模型的元数据Meta
除了抽象模型，在模型中定义的字段都会成为表中的列。

如果我们需要给模型指定其他一些信息，例如排序方式、数据库表名等，就需要用到 Meta。Meta 是一个可选的类，具体用法如下：

```python
class Author(models.Model):
    name = models.CharField(max_length=40)
    email = models.EmailField()

    class Meta:
        managed = True
        db_table = 'author'
```

不知你是否对上述代码有影响。通过 Django 将数据库表反向生成模型时，Django 会默认带上 managed 和 db_table 信息。

我主要说下 Meta 一些重要的属性，其他属性你可以通过文档信息进行学习。

**abstract**: 如果 abstract = True，模型会指定为抽象模型。它相当于面向对象编程中的抽象基类。

**proxy**：如果设置了proxy = True，表示使用代理模式的模型继承方式。

**db_table**：指定当前模型在数据库的表名。

**managed**：该属性默认值为 True，表示能创建模型和操作数据库表。

**ordering**：指定该模型生成的所有对象的排序方式。默认按升序排列，如果在字段名前加上字符 “-” 则表示按降序排列，如果使用字符问号 “？” 表示随机排列。
```python
ordering = ['pub_date']             # 表示按'pub_date'字段进行升序排列
ordering = ['-pub_date']            # 表示按'pub_date'字段进行降序排列
ordering = ['-pub_date', 'author']  # 表示先按'pub_date'字段进行降序排列，再按`author`字段进行升序排列。
```
**verbose_name**：给模型设置别名。如果不指定它，Django 会使用小写的模型名作为默认值。
```python
verbose_name = "book"
verbose_name = "图书"
```

**verbose_name_plural**：因为英语单词有单数和复数两种形式，这个属性是模型对象的复数名。中文则跟 verbose_name 值一致。如果不指定该选项，那么默认的复数名字是 verbose_name 加上 ‘s’ 。
```python
verbose_name_plural = "books"
verbose_name_plural = "图书"
```

**indexes**：为当前模型建立索引列表。用法如下：
```python
from django.db import models

class Customer(models.Model):
    first_name = models.CharField(max_length=100)
    last_name = models.CharField(max_length=100)

    class Meta:
        indexes = [
            models.Index(fields=['last_name', 'first_name']),
            models.Index(fields=['first_name'], name='first_name_idx'),
        ]
```



## 2.模型的继承
根据模型的 Meta 信息设置，模型继承方式可以分为三种：

### 2-1.抽象模型
模型的 Meta 类中含有 abstract = True 属性。抽象模型一般被当作基类，它持有子类共有的字段。值得注意的是，抽象模型在数据库中不会生成表。
```python
from django.db import models

# 抽象模型
class Person(models.Model):
    name = models.CharField(max_length=500)
    age = models.PositiveIntegerField()
    
    class Meta:
        abstract = True

# 子模型
class Student(Person):
    school_name = models.CharField(max_length=20)
```

子模型如果没有定义 Meta 类，那么会继承抽象模型的 Meta 类。但是 abstract 属性不会被继承。

### 2-2.多表继承
这种方式继承方式，子模型的父模型可以一个或者多个。

当父类模型是正常的模型，即不是抽象模型，在数据库中有对应表。

虽然在 Model 层不推荐使用多重继承，但 Django 的 ORM 还是支持这样的使用方式。

如果使用多表继承，子模型跟每个父模型都会添加一个一对一的关系。

```python
from django.db import models

# 父模型 one
class Model_One(models.Model):
    attr1 = models.CharField(max_length=10)

# 父模型 two
class Model_Two(models.Model):
    attr2 = models.CharField(max_length=10)

# 子模型
class Multiple(Model_One, Model_Two):
    attr3 = models.CharField(max_length=10)
```
多重继承的时候，子类的 ORM 映射会选择第一个父类作为主键管理，其他的父类作为一般的外键管理。


### 2-3.代理模型
使用多表继承时，父类的每个子类都会创建一张新数据表。但是我们只是想扩展一些方法，而不想改变模型的数据存储结构。

我们可以将在 Meta 类中增加约束proxy=True 来实现。

此时**子模型**称为**父模型**的**代理类**，子类中只能增加方法，而不能增加属性。
```python
from django.db import models
from django.contrib.auth.models import User

class Person(User):
    name = models.CharField(max_length=10)
    
    class Meta:
        proxy = True

    def do_something(self):
        pass

class Man(Person):
    job = models.CharField(max_length=20)

class Woman(Person):
    makeup = models.CharField(max_length=20)
```
