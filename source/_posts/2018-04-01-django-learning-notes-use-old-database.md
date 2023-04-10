---
layout: post
title:  "Django 学习笔记之使用旧数据库"
date:   2018-04-01 17:24:36
urlname: 43
cover: https://img.jikehou.cn/cover/2018-04-01.jpg
categories: [从零学Django]
tags: [Python, Web, Django, 数据库]
keywords: [Python, web, Django, 数据库]
---
如果你按照顺序，从第一篇文章读到本文。恭喜你，你已经将 Django 大部分基础知识掌握了。

后续的文章是在之前的基础上添砖加瓦或常用的应用。本文将的内容是一个场景应用，新项目使用旧数据库。

可能以前项目是使用其他语言，如 Java 或 PHP 开发的，后面迁移到 Python 上。虽然应用程序改变了，但是数据缺不是丢弃。

因此，存在这样的问题。那就是使用 Django 开发的 Web 应用程序如何使用旧的数据库？

我就使用旧的 SqLite 数据库作为例子进行讲解，MySQL 等其他数据库也是操作类似。
<!-- more -->
## 1.导入数据库

旧的数据库名为 MyDataBase.db, 我将其导入到新项目的 db 目录。

![我将其导入到新项目的 db 目录](https://img.jikehou.cn/img/71_1.png)

然后将 settings.py 文件中的数据库名称修改下。
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        # 系统自动生成
        # 'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
        'NAME': os.path.join(BASE_DIR, './db/MyDataBase.db'),
    }
}
```

## 2.创建模型
我们知道 Models 层是跟数据库打交道的层次，需要创建数据库表对应的 models。

但对应使用旧数据库，我们不需要手动创建，可以使用 Django 提供的 API 反向生成 models。

假设数据有一张表，其创建表的 SQL 语句如下：
```sql
Create table if not exists `Users` (
     `id` INTEGER PRIMARY KEY,
     `username` varchar(64),
     `password` varchar(64),
     `email` varchar(64) ,
     `QQ` varchar(15) ,
     `weibo` varchar(300),
     `cell_phone` varchar(20),
     `college` varchar(60),
     `living_place` varchar(200),
     `remarks` varchar(400)
     );
```


我们进入到项目的目录下，打开终端，执行以下命令：
```python
python manage.py inspectdb
```

目的是针对已有数据库自省生成新的 models。
![目的是针对已有数据库自省生成新的 models](https://img.jikehou.cn/img/71_2.png)

然后执行导出命令，将模型导出到 models.py 文件中。
```python
python manage.py inspectdb > models.py
```

你会看到项目根目录下多了一个名为 models.py 文件。
![models.py 文件](https://img.jikehou.cn/img/71_3.png)

将其内容迁移到 app 的 models.py 中。

默认配置下生成不可修改或删除的 models，所以我们修改 meta class 中的 managed 属性。

如果 managed 被设置为 True，则告诉 Django 可以对数据库进行操作。

最后一步，同步 model 的改动到数据库中。在项目目录下，使用终端执行以下命令。
```python
python manage.py migrate
```

如果没有报错的话，证明成功导入。有时候可能会报出以下的错误：
!有时候可能会报出以下的错误[](https://img.jikehou.cn/img/71_4.png)

原因是表中定义了 id 字段，同时这个字段被设定为主键。

具体的解决方式是：修改 model.py 中 id 字段的定义; 将其中的 **null=True** 修改为 **primary_key=True**。

```python
# 修改前
id = models.IntegerField(blank=True, null=True)

# 修改后
id = models.IntegerField(blank=True, primary_key=True)
```

保存修改之后，重新执行同步数据库命令即可。