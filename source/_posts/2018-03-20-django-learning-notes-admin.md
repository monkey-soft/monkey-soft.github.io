---
layout: post
title:  "Django 学习笔记之后台管理"
date:   2018-03-20 20:49:34
urlname: 41
cover: https://img.jikehou.cn/cover/2018-03-20.jpg
categories: [从零学Django]
tags: [Python, Web, Django]
keywords: [Python, web, Django]
---
本文是 Django 学习笔记系列的第七篇。前面 6 篇文章，我们已经了解了 Django MTV 模型中三个层的内容。

这部分内容算是最基础，也是最重要。

本文的内容相对简单，阅读起来会比较轻松些。

主要是介绍下 Django 默认管理后台以及一些实用后台管理系统的第三方应用。
<!-- more -->
## 1.前言

每个网站无论大小，大型电商网站也好，个人博客也罢，它们都是一个管理后台。

管理后台可以看做一个窗口，管理员通过它来管理以及维护网站。 

Django 作为一个全能型的框架，当然也自带了一个后台管理系统。

登录后台管理希望能对前端或者数据库数据进行增加、修改、删除等工作。我们现在就激动该系统来学习。

## 2.激活管理界面

其实 Django 默认帮我们激活 admin 管理后台。

不知你还记得上次的操作？ 当新建创建应用的，需要将刚创建的 app 加入到 setting.py 文件中。

在 setting.py 文件中，你会看到前面有很多应用。

```python
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

其中 django.contrib.admin 就是管理后台。

因此，我们可以知道 admin 其实也是一个应用，只不过它是 Django 自带的。

我们只需要做一件事，那就是创建用户。在项目目录下，打开终端，执行以下命令来创建超级管理员。

```
python manage.py createsuperuser
```

打开终端之后，依次输入用户名，邮箱和密码即可创建。成功创建换管理员结果如下：
![成功创建换管理员结果](https://img.jikehou.cn/img/20180320_1.png)

Django 在密码校验这块做的还不错。我使用弱密码 'admin'和'1234678'，Django 都不让我通过。

成功创建超级用户之后，使用浏览器访问后台管理系统： `127.0.0.1:8000/admin`。

![后台管理系统](https://img.jikehou.cn/img/20180320_2.png)

输入用户名和密码并进行登录，会看到以下界面。因为是空项目，所以没有数据，只有显示用户账号的信息。

你可以对 admin 进行二次开发，这样你就能在后台对数据库数据进行操作。
![能在后台对数据库数据进行操作](https://img.jikehou.cn/img/20180320_3.png)

## 3.常用的 admin 应用

我推荐这几个应用都是第三方开源项目，都支持 Django 2.0 版本。具体安装方法可以阅读各个插件的 Github 仓库地址。

### 3-1. `Xadmin`

Xadmin 在 Django 后台管理系统应用库中算是名声显赫。它算是一款内置功能比较丰富的框架，提供了基本的CRUD功能，还内置了丰富的插件功能；还包括数据导出、书签、图表、数据添加向导及图片相册等多种扩展功能。

最重要的是，它使用起来非常方便。我们只需要定义数据的字段等信息，即可获取一个功能全面的管理系统。

推荐指数：★★★★★

{% btn 'https://github.com/sshwsfc/xadmin/', Xadmin 传送门, far fa-hand-point-right,blue larger %}


### 3-2. `django-grappelli`

django-grappelli 可以算是一个功能强大的应用。它使用网格状的形式来呈现数据，这个给人简洁大方的感觉。因此，django-grappelli 更加适合需要对数据频繁操作的场景。

推荐指数：★★★★

{% btn 'https://github.com/sehmaschine/django-grappelli/', django-grappelli 传送门, far fa-hand-point-right,blue larger %}


### 3-3. `django-material`

django-material 是采用谷歌 Material Design 来设置 UI 。自己比较喜欢 MD 这种风格的界面，所以推荐给大家。

推荐指数：★★★☆


{% btn 'https://github.com/viewflow/django-material', django-material 传送门, far fa-hand-point-right,blue larger %}


### 3-4. `django-admin-bootstrap`

django-admin-bootstrap 是一个能自动隐藏侧面菜单栏的响应式管理后台。它跟 Xadmin 一样，都是基于 bootstrap 开发的。个人觉得比较适合初学者来学习和研究。

推荐指数：★★★

{% btn 'https://github.com/douglasmiranda/django-admin-bootstrap', django-admin-bootstrap 传送门, far fa-hand-point-right,blue larger %}