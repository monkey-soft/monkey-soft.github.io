---
layout: post
title:  "阅读优秀代码是提升技术的最佳途径"
date:   2018-12-02 16:36:29
urlname: 90
cover: https://img.jikehou.cn/cover/2018-12-02.jpg
categories: [个人成长]
tags: [Python, 个人成长]
keywords: [Python, Flask, Django, web, 源码, Github]
description: 阅读优秀代码是提升技术的最佳途径
---
我们身在行业中，要不断地学习提高自己的能力。

有一种不错的方式来提高自己的技术实力。

那就是阅读别人优秀的代码。

那也许你会有疑问，为何要阅读别人优秀的代码？而不是拿到代码就进行阅读？

正如意大利作家伊塔洛·卡尔维诺的`《为什么要读经典》`中提到`经典于对喜欢它的人构成一种宝贵的经验`。
<!-- more -->
因此，阅读别人的优秀源代码有很多好处，收益也会非常大。

通过大量阅读别人的代码，我们可以采用更先进的方法、风格和架构模式，让自己的技术能力和知识不断的增长。

我收集一些优秀的 Python Web 相关的优秀项目，分享给大家参考学习。

## 1.flask-admin
flask-admin 是基于 flask 框架开发的 admin 管理系统。

该库能基于现有的数据模型，快速创建管理界面。

易用性很高，简单配置参数就能运行。

运行结果如下：

![运行结果](https://img.jikehou.cn/img/124_1.gif)

另外，它还是微服务应用，提供很多 API。我们能从中学到微服务架构设计思路、数据(model)-视图(view) 绑定设计等。

{% btn 'https://github.com/flask-admin/flask-admin', Github 仓库地址, far fa-hand-point-right,blue larger %}


## 2.FlaskBB
FlaskBB 基于 Flask 框架做的论坛，轻量级的论坛应用。

虽然具备论坛帖子分类、成员/成员组地管理、搜索功能、插件等功能，但是功能还是有限，做得不够完善。

值得庆幸的，我们可以在这个项目上进行二次开发，实现更加复杂的功能。

![FlaskBB](https://img.jikehou.cn/img/124_2.png)

阅读该仓库代码，我们了解到论坛的设计和开发等各方面的知识。

{% btn 'https://github.com/flaskbb/flaskbb', Github 仓库地址, far fa-hand-point-right,blue larger %}


## 3.superset
superset 是一个 Apache 开源的现代的、企业级商业智能 web 应用程序。

它基于 flask-appbuilder 框架开发的。superset 的可视化能力超强，我们可以用其来做数据分析、展示和探索。

![superset](https://img.jikehou.cn/img/124_3.png)

这个项目实在太优秀了，如果能啃下了，能力绝对提升不止一个档次。

{% btn 'https://github.com/apache/incubator-superset', Github 仓库地址, far fa-hand-point-right,blue larger %}


## 4.django-blog-tutorial
django-blog-tutorial 项目是基于 Django 1.10 编写的博客网站。

另外，它还是一个教程仓库。

它通过 26 篇教程一步步带我们使用 Django 从零开发一个个人博客系统。

![django-blog-tutorial](https://img.jikehou.cn/img/124_4.png)


{% btn 'https://github.com/zmrenwu/django-blog-tutorial', Github 仓库地址, far fa-hand-point-right,blue larger %}


## 5.jumpserver
Jumpserver 是一款基于 Django 框架编写开源的跳板机(堡垒机)系统，实现了跳板机应有的功能。

基于ssh 协议来管理，客户端无需安装 agent。

该仓库很适合运维工程师阅读学习。

![jumpserver](https://img.jikehou.cn/img/124_5.png)


{% btn 'https://github.com/jumpserver/jumpserver', Github 仓库地址, far fa-hand-point-right,blue larger %}