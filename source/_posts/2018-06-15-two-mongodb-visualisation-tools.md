---
layout: post
title:  "安利两个 MongoDB 可视化工具"
date:   2018-06-15 21:53:08
urlname: 61
cover: https://img.jikehou.cn/cover/2018-06-15.jpg
categories: [数据库]
tags: [数据库, MangoDB]
keywords: [数据库, MangoDB, Nosqlclient, Mongo Plugin]
---
MongoDB 是一种 NoSQL 数据库。

NoSQL(Not Only SQL )，意即"不仅仅是SQL", 泛指非关系型的数据库。

这两种类型差别之一是存储方式。

关系数据库以键值对存储，它的结构不固定。而关系型数据库以行和列的二维表格形式来存储数据。

所以非关系型数据库（如 MongoDB）不支持标准的 SQL 的语法。

如果我们刚接触 Mongo 数据库，对语法还不熟悉，想通过可视化方式来操作。

那么，今天我就给大家推荐两个 Mongo 可视化操作工具。
<!-- more -->
## 1.Mongo Plugin

大多数 Python 程序员使用的编辑器是 Pycharm。因为 Pycharm 是一个非常人性化的 IDE 工具。

Pycharm 工具自带支持 MySQL、SQLite 等数据库的可视化工具。

但是还不支持 Mongo 数据库。

国外一位大神 dboissier 专门为 Pycharm 开发了 一款插件。它的就是 **Mongo Plugin**。

这让我们可以在 Pycharm 上对 mongo 数据库进行管理。

安装该插件也很方便，直接在 Pycharm 的【settings】-【Plugin】选项中搜索 mongo 即可安装。

![安装](https://img.jikehou.cn/img/92_1.png)

然后在【settings】中找到【Mongo Servers】，在配置下 MongoDB 的安装路径。

然后填写连接的数据库配置信息，就可以操作数据库了。

![配置安装路径](https://img.jikehou.cn/img/92_2.png)

![操作数据库](https://img.jikehou.cn/img/92_3.png)

成功连接数据库之后，就可以对 mongo 的 Collections（相当于 MySQL 的 表）进行操作。

通过下图，我们了解到数据是以 Key-Value 形式展示。

我们也可以直接对数据进行修改或者删除。

![数据是以 Key-Value 形式展示](https://img.jikehou.cn/img/92_4.png)

**推荐原因**：
- 它是 Pycharm 的插件。所以我们可以直接在 Pycharm 操作，方便快捷。
- 可以图形化显示数据。
- 支持 增删改查 基本操作。


## 2.Nosqlclient

Nosqlclient 是一个跨平台的免费的 MongoDB 管理工具。

因为它是由有 Node.js 编写的，相当于一个 web 应用程序，所以我们可以直接将其部署到服务器上。

如果我们使用 Windows 系统或者 Mac 系统，我们不需要安装 Node.js 环境，再运行 Nosqlclient。

官方团队已经为我们提供安装包，我们只需下载安装，然后即可直接运行。

{% btn 'https://github.com/nosqlclient/nosqlclient', Nosqlclient 下载地址, far fa-hand-point-right,blue larger %}


界面采用“简约”的风格，给人一种清爽，舒服的感觉。

数据库监控界面，可以监控当前内存使用情况，数据读写情况

![数据库监控界面](https://img.jikehou.cn/img/92_5.png)

数据库管理界面，支持管理用户，导出/导入数据等

![数据库管理界面](https://img.jikehou.cn/img/92_6.png)

工具界面，直接命令行操作数据库，分析 Collections（相当于 MySQL 的 表）的情况等。我最喜欢这个功能。

![直接命令行操作数据库](https://img.jikehou.cn/img/92_7.png)

数据管理，对数据进行增删改查操作。

![数据管理](https://img.jikehou.cn/img/92_8.png)

**推荐原因：**
- 界面友好、直观，同时操作方便
- 有对内存和数据库数据的实时监控功能
- 以图表形式即时展示数据库读/写情况
- 支持数据库数据导入导出
- 支持 SSH 远程连接
- 模式分析