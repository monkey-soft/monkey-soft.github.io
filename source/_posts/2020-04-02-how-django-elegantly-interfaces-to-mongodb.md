---
layout: post
title:  "说说 Django 如何优雅地对接 Mongodb"
date:   2020-04-02 16:23:34
urlname: 116
cover: https://img.jikehou.cn/cover/2020-04-02.jpg
categories: [Python编程]
tags: [Python, 数据库, MongoDB, Web开发]
keywords: [Django, Python, Djongo, MongoDB]
---
大家好，我是猴哥。

近来在研究 Django 对接 MongoDB 数据库，遇到一些坑，自己随便做下总结。
<!-- more -->

## 1.前言

Django 更新迭代速度真的是快，现在最新版本都 3.0.5 了。如果有留意 Django 在 GitHub 上的仓库，不难发现几乎每天都有人在提交。

![Django 更新迭代速度真的是快](https://img.jikehou.cn/img/20200402_1.png)

不得不能说 Django 官方团队好积极呀。

软件的版本更新快，其实也算是一件好事。如果我们在使用过程中发现问题和缺陷，提交给团队，能很快得到修复。但这不意味着我们要跟着版本更新的节奏走，建议根据自身情况来定。

个人研究和学习，使用最新版本倒不是问题。

如果是公司或者团队要用于商业，特别是已经上线的，最好是求稳。选择官方有长期支持版本，或保持跟最新版本有两~三个版本差。

目前，Django 团队对各个版本的支持情况。

![各个版本的支持情况-1](https://img.jikehou.cn/img/20200402_2.png)

![各个版本的支持情况-2](https://img.jikehou.cn/img/20200402_3.png)

不难看出，Django 3.0 版本也算是一个过渡版本。

3.0.5 在今年 8 月份就停止主流维护支持，到明年 4 月份就停止维护了。

具体是什么意思？说人话。

3.0.5 版本从现在到 2020 年 8 月份，再这段时间内版本更新迭代会比较快，既有实现新需求，又要修复遗留的重大缺陷。

8 月份一过，就不做新需求了，偶尔修修 bug 而已，版本更新截止放慢了。

因此，个人学习选择长期支持的 2.2 版本或尝鲜的 3.0 版本都行。

如果要开发并用于商用，推荐选择长期支持的 2.2 版本。

同时，Django 2.2 已经不再支持 Python 2.x 和 3.x 版本，最低要求 Python 版本是 3.5。

## 2.选型

Django 本身已经有 ORM 框架。ORM 是对象关系映射(Object Relational Mapping)的缩写，由于程序设计者更多采用面向对象的思想，而数据库则以关系作为其基础。

ORM 的作用使得我们可以采用面向对象的思路来设计数据库，使数据库设计更加简单。

但是 Django 框架的数据库引擎中没有 MongoDB 引擎，配置文件 setting.py 中的 `ENGINE` 字段只支持常见几种关系型数据库。
```python
django.db.backends.sqlite3
django.db.backends.mysql
django.db.backends.oracle
django.db.backends.postgresql
django.db.backends.postgresql_psycopg2
```

如果没有数据引擎支持，我们会多做很多造轮子的活，比如实现数据库连接、封装数据库 DAO 接口等。

我在 Django 官网 WIKI 文档中了解到，Django 也是支持非关系型数据库，不过需要使用第三方支持库。官方解释到，如果数据库使用 MongoDB，推荐使用 **Djongo**这个库来做数据库引擎。

我们不用担心 Djongo 不够完善，该库已经有在超过 1 百万人从 pypi 上下载并使用。

再者，Djongo 没有大刀阔斧地修改，保留 Django ORM 框架，这也算是比较稳。

## 3.优雅地使用

### 3-1.安装

使用 pip 安装 python 第三方库是最方便的。
```python
pip install djongo
```

### 3-2.修改配置

在项目的 setting.py 中，修改数据库引擎、数据库名、主机号等信息。Djongo 关于数据库完整的配置：
```python
#  setting.py
DATABASES = {
    'default': {
        'ENGINE': 'djongo',
        'ENFORCE_SCHEMA': True,
        'LOGGING': {
            'version': 1,
            'loggers': {
                'djongo': {
                    'level': 'DEBUG',
                    'propogate': False,                        
                }
            },
         },
        'NAME': 'your-db-name',
        'CLIENT': {
            'host': 'host-name or ip address',
            'port': port_number,
            'username': 'db-username',
            'password': 'password',
            'authSource': 'db-name',
            'authMechanism': 'SCRAM-SHA-1'
        }
    }
}
```

如果是本地安装 MongoDB 数据的话，可以不用配置这么多参数。

```python
#  setting.py
DATABASES = {
    'default': {
        'ENGINE': 'djongo',
        'ENFORCE_SCHEMA': True,
        'NAME': '数据库名称',
        'CLIENT': {
            'host': '127.0.0.1',
        }
    }
}
```
MongoDB 数据库默认访问端口是 27017。如果你修改访问端口，需要显示指定端口号。用户名和密码也不需要填写，MongoDB 默认没有开启用户验证。

如果你想开启用户校验或者线上数据库，需要在数据库安装目录下，找到 mongod.cfg 文件，然后开启登录校验。
```
# mongod.cfg 文件中找到以下字段
#security:
authorization: enabled
```
配置好文件之后需要重启数据库，后面链接数据库就需要账号和密码了。

