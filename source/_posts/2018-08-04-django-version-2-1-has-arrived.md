---
layout: post
title:  "Django 2.1 版本已经来袭"
date:   2018-08-04 16:18:59
urlname: 72
cover: https://img.jikehou.cn/cover/2018-08-04.jpg
categories: [从零学Django]
tags: [Python, Web, Django]
keywords: [Python, web, Django]
---
一个备受关注、很多人都在使用的软件，软件开发团队会定期发布新版本。

其主要目的是修复之前一些 Bug 以及新增一些新特性。

Django 作为主流的 Python Web 开发框架，当然也不例外。

在几天前，即 8 月 1 号，Django 官方团队发布 Django 2.1 版本。

新版本发布，我们可以不用急于更新替换，但要关注新版本的发布内容。

我看了下官方的发布公告，总结几个重要的点。
<!-- more -->
## 1.Python 版本支持

**Django 2.1 将不再支持 Python 3.4**，最低要求 Python 版本是 3.5。

这也意味着 Django 2.0 是最后一个支持 Python 3.4 的版本。

## 2.模型新增查看权限

这个功能，很多开发者都期待了很久。盼星星盼月亮，终于盼来了。

在以前的版本中，模型（Model）只有“增删改”权限。新版本增加查看（View）权限。这也意味着在 Model 的 Meta 配置选项中， `default_permissions` 属性默认值变为 `('add', 'change', 'delete', 'view')`。

此外，官方团队考虑到向前兼容性。

在旧版本中，如果用户取得修改（change）权限。

更新到新版本，用户会自动获得查看（View）权限。

View 权限同样也适用于 admin，它可以给用户指定只读权限。

这里是通过 `ModelAdmin.has_view_permission(request, obj=None)` 来设置。如果希望一个 obj 被设置成只读属性，那么要返回 True。


## 3.模型
新版本的 Model 类也一些特性，我也列举出比较重要的点。
- Model 模型类支持 `__init_subclass__` 重写
- 查询表达式终于支持`负号查询`了。
- 在模型的表单中，`BinaryField` 可以被设置为 `editable=True`。

## 4.表单
表单（Forms）中的 ImageField 组件新增 `accept="image/*"` 属性。

## 5.缓存
基于内存缓存（local-memory cache backend）采用`最近最少使用（LRU）` 筛选策略，以替换之前随机数的策略。

## 6.数据库版本支持

- MySQL 数据库
Django 2.1 不再支持 MySQL 5.5 以及以下版本，只能选择 5.6 或者更高版本。

- PostgreSQL 数据库
Django 2.1 支持 PostgreSQL 最低版本为 9.4.

- SQLite 数据库
新版本不再支持 < 3.7.15 版本的 SQLite。