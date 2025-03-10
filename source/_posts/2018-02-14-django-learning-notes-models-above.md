---
layout: post
title:  "Django 学习笔记之模型(上)"
date:   2018-02-14 14:48:41
urlname: 39
cover: https://img.jikehou.cn/cover/2018-02-14.jpg
categories: [Python编程]
tags: [Python, Web开发]
keywords: [Python, web, Django]
---
上篇文章讲解模板。

你本文将讲解 “MTV” 中 M 层次，即模型层（数据存取层）。

模型这内容比较多，我将其拆分为 3 个部分来讲解。

同时，文章也配套了例子，你可以通过 **阅读原文** 来查看。
<!-- more -->
## 1.编程环境
因为 Django 近期推出 Django 2.0 版本， 所以有必要再说明下。

如果你是按照本系列来学习 Django 框架的话，按照前面安装 Django 的方式，你安装 Django 版本应该是最新版本，即 2.0。

那么使用最新 Django 版本来学习可以吗？如果是学习的话，不用太在意版本。

当然学习最新的较好，因为可以学习新的 API。

同时，Django 2.0 不再兼容 Python 2 了，现在学习 Python 都建议采用 Python 3版本了。

另外 Django 1.8 官方只维护到 2018 年的 4 月，1.11 是最后一个兼容 Python 2 的 Django版本。

如果是项目需要升级 Django版本，需要兼容到 Python 2，那么要考虑用 1.11 版本了。

顺便补充下本文用的一些工具的版本：Python 版本是 3.6，Mysql 版本是 5.5.


## 2.模型是什么
在 Web 应用中，数据一般存储到数据库中

Django 中的模型层是跟数据库打交道的层次。

模型层中可能会有多个模型，每个模型（每个 app 中的 models.py 中每个类都是一个模型）都对应着数据库中的唯一一张表。


## 3.配置数据库
在我们探索 Django 的模型层之前，我们需要配置下数据库；

告诉 Django 视野什么数据库以及如何连接数据库。

这一步要确保配置无误，不然后面难以执行。

我们找到新项目中的 setting.py, 里面有个 **DATABASES** 选项。

Django 默认是使用 sqlite 数据库，所以你会看到里面 sqlite 数据库的配置信息。

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}
```

上面的代码中的 **ENGINE** 是选择哪个数据库引擎, **NAME** 是数据库的名字。

至于选择哪种引擎，要看项目中使用了什么数据库。

Django 目前支持以下这 MySQL、PostgreSQL、Oracle 等数据库，它们的数据库引擎设置如下：

|  设置字段    |    数据库 | 设配器(驱动)  |
| :-------- | :--------| :-- |
| postgresql  | PostgreSQL  |  psycopg 1.x版   |
| postgresql_psycopg2 |   PostgreSQL  |  psycopg 2.x版  |
| mysql      |    MySQL  | MySQLdb  |
| sqlite3      |   SQLite  | 不需要  |
| oracle      |   Oracle  | cx_Oracle  |

其中设置字段是填充 ENGINE 的值。

如果你使用的 MySQL 数据库，那么你需要填写 django.db.backends.mysql。

数据库驱动表示需要使用 pip 安装该库。

如果你使用的 MySQL 数据库，那么你需要安装 MySQLdb 设配器。

但是这里有个坑，MySQLdb 在支持 Python 2 版本，不支持 Python 3 版本。所以你安装该设配器之后，运行项目会报出错误。

Django 官网建议使用替代品 **mysqlclient**。mysqlclient 是 MySQLdb 的一个分支，最主要是它支持 Python 3。

本文中使用到 Mysql 数据库，那么 DATABASES 的配置如下：
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'django',
        'HOST': '127.0.0.1',
        'PORT': '3306',
        'USER': 'root',
        'PASSWORD': '123456',
    }
}
```


## 4.第一个模型
我们先新建名为 Django_demo 的 projeact, 再新建名为 demo 的 app。

最后，别忘记在 setting.py 中将新创建的 app 激活。
```python
# Django 2.0 的初始配置内容
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'demo',  # 我们新创建的 app
]
```

### 4-1.创建模型
打开我们刚才创建的 app 中的 models.py 文件，我们以后就主要在这里编写模型。

假设现在有个需求：某个作者创作了一本书，本书由出版社出版了。我们可以设定三者的字段以及关系。

- 假设作者有姓名、Email 邮箱这两个数据属性。
- 假设出版社有出版社名称、地址这两个属性。
- 书籍有两四个属性：书名、出版日期、作者、出版社。有一个或多个作者（和作者是多对多的关联关系[many-to-many]）， 只有一个出版商（和出版商是一对多的关联关系[one-to-many]，也被称作外键[foreign key]）

所以我们编写代码如下：

```python
from django.db import models

class Author(models.Model):
    name = models.CharField(max_length=40)
    email = models.EmailField()

class Publisher(models.Model):
    name = models.CharField(max_length=30)
    address = models.CharField(max_length=50)

class Book(models.Model):
    title = models.CharField(max_length=100)
    authors = models.ManyToManyField(Author)
    # publisher = models.ForeignKey(Publisher)
    # Django 2.0 需要加上 on_delete=models.CASCADE
    publisher = models.ForeignKey(Publisher, on_delete=models.CASCADE)
    publication_date = models.DateField()
```


我们定义每个模型，即类（如 Author 和 Book）都需要继承 django.db.models.Model。

Model 是 Django 做了一层包装以便我们更加方便地使用的类， 它其中包含了所有数据库交互的方法。

上面代码中的每个类相当于单个数据库表，每个属性也是这个表中的一个字段。 

属性名就是字段名，它的类型（例如 CharField ）相当于数据库的字段类型 （例如 varchar ）。

例如， Publisher 模块等同于下面这张表
```sql
CREATE TABLE "Publisher" (
    "id" serial NOT NULL PRIMARY KEY,
    "name" varchar(30) NOT NULL,
    "address" varchar(50) NOT NULL,
);
```

所以我们在为属性命名的时候，要避免使用数据库的关键字。

另外，我们只需要关心每个类的属性以及长度限制，不用关心怎么创建数据库表。

Django 可以自动生成这些 CREATE TABLE 语句的。


### 4-2.创建数据表
我们上面的创建了几个模型还处于定义上，Django 还没有正真创建数据库中的表。

因此，我们需要执行两个命令来同步一下数据库。


在我们刚才创建的工程 Django_demo 目录下，我们打开终端，执行以下命令：
```shell
python manage.py makemigrations
```

运行成功效果图如下：

![运行成功效果图](https://img.jikehou.cn/img/20180214_1.png)

这一步相当于 在该app下建立 migrations目录，并记录下你所有的关于modes.py的改动，比如0001_initial.py， 但是这个改动还没有作用到数据库文件


第二步，在之前的终端上继续执行 migrate 命令。

```shell
python manage.py migrate
```

运行成功效果图如下：
![运行成功效果图](https://img.jikehou.cn/img/20180214_2.png)

这一步表示将该改动（当makemigrations之后产生了0001_initial.py 文件）作用到数据库文件，比如 create table 之类。


如果你用到 Pycharm 的 Database 功能，你会看到我们刚才创建的定义的几个模型。
![运行成功效果图](https://img.jikehou.cn/img/20180214_3.png)


### 4-3.字段类型
在前面的例子中，我们需要对每个属性设置一个字段，例如 CharField。Django 内置了几十种内置字段类型。常用的类型如下：

1) **AutoField**：一个根据实际ID自动增长的 IntegerField 。如果表中没有设置主键时，将会自动添加一个自增主键。

2）**IntegerField**：一个整数。在 Django 所有支持的数据库中，-2147483648 到 2147483647 范围才是合法的。

3）**BigIntegerField**：一个64位整数, 和 IntegerField 类似，不过它的范围比较大。

4）**BooleanField**：一个 true/false 字段。这个字段的默认表单部件是 CheckboxInput。

5）**CharField**：字符字段。对于比较大的文本内容,请使用 TextField 类型。这个字段的默认表单部件是 TextInput。它有个参数 max_length。max_length 表示字段允许的最大字符串长度。这将在数据库中和表单验证时生效

6）**TextField**：大文本字段。默认的表单部件是一个 Textarea。

7）**DateField**：日期。它带有两个可选参数：auto_now 和 auto_now_add。auto_now 表示当对象保存时，该字段会自动设置成当前时间。一般用于记录“修改时间” 。auto_now_add 记录字段首次被创建的时间。

8）**DateTimeField**：时间和日期。它也带有两个可选参数，名字和用法跟 DateField 一样。

9）**TimeField**：时间字段, 类似于Python datetime.time 实例. 和 DateField 具有相同的选项。

10）**URLField**：一个 CharField 类型的URL，默认长度是200；默认的表单部件是一个 TextInput。

11）**EmailField**：一个检查输入的email地址是否合法的 CharField 类型。

12）**FileField**：上传文件字段。

13）**ImageField**：图片字段，它继承了 FileField 所以属性和方法。


### 4-4.关系字段
关系字段(Relationship fileds) 也是属于字段，只不过三个字段比较特殊，所以单独拿出来说。

我们按照上述的创建模型的例子来继续讲解。

它们三者之间的关系应该这样：一本书由一家出版社出版，一家出版社可以出版很多书。

一本书由多个作者合写，一个作者可以写很多书。

1）*ForeignKey*
表示属于模型间关系中的多对一关系。

在我们的范例模型中，一家出版社 publisher 可以出版很多书 Book。

在数据库中, Django 使用 ForeignKey 字段名称＋ "_id" 做为数据库中的列名称。

在上面的例子中, 书籍 model 对应的数据表中会有一个 publisher_id 列。

你可以通过显式地指定 db_column 来改变该字段的列名称，不过，除非你想自定 义 SQL ，否则没必要更改数据库的列名称。

它第一个参数必须传入该模型关联的类。on_delete 现在可以用作第二个位置参数(之前它通常只是作为一个关键字参数传递). 

在Django 2.0中，这将是一个必传的参数。


2）*OneToOneField*
它属于 ForeignKey 中的特例。

当 ForeignKey 中有个字段 unique 被设置为 True 时， 就表示一对一关系。

3）*ManyToManyField*
属于模型间关系中的多对多关系。

在我们的范例模型中， Book 有一个 多对多字段 叫做 authors。

因为他们的关系是一本书由多个作者合写，一个作者可以写很多书。

在数据库中 Django 创建一个中间表来表示 ManyToManyField 关系。

默认情况下，中间表的名称由两个关系表名结合而成。

所以刚才我们创建数据库表的途中，会有四张表，而不是三表。

### 4-5.字段选项
有些字段会有些特殊参数，但所有字段类型都又些通用的可选选项。先是常用的可选选项。

1）**null** ：如果该参数设置为 True，Django将会把数据库中的空值保存为 NULL。不填写就默认为 False。

2）**blank**：如果为 True ，该字段允许为空值，不填写默认为 False。这个字段是用于处理表单数据输入验证。

3）**primary_key**：如果为 True，那么这个字段就是模型的主键。

4）**unique**：如果该值设置为 True, 这个数据字段在整张表中必须是唯一的。

5）**default**：设置该字段的默认值。

6）由二项元组构成的一个可迭代对象（列表或元组），用来给字段提供选择项。 

如果设置了 **choices**，默认的表单将是一个选择框。具体使用例子如下：
```python
from django.db import models

class Person(models.Model):
    SHIRT_SIZES = (
        ('S', 'Small'),
        ('M', 'Medium'),
        ('L', 'Large'),
    )
    name = models.CharField(max_length=60)
    shirt_size = models.CharField(max_length=1, choices=SHIRT_SIZES)
```

下篇文章，我们将讲解如何对这些模型（表）进行操作。

## 5.源码

如果你想获取项目的源码, 点击按钮进行下载。

{% btn 'https://github.com/monkey-soft/Django_demo', 源码下载, far fa-hand-point-right,blue larger %}