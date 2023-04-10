---
layout: post
title:  "计划分享Python Web学习心得"
date:   2018-01-27 15:29:44
urlname: 34
cover: https://img.jikehou.cn/cover/2018-01-27.jpg
categories: [从零学Django]
tags: [Python, Web]
keywords: [Python, web]
---
前段时间，有个读者留言跟我说，有空出使用 Python 实现 RESTful API 的教程。我一看，这正合我意。

自己很早就想学习 Python web。

之前有简单过了解些 Django 框架基础知识。但对于 Python Web，我还是研究不够深入。

因此，打算接下来一段时间。自己学习 Python Web，并将学习心得分享出来。

自己在 Web 方面是只菜鸟，所以请老鸟轻喷。
<!-- more -->
回到刚才话题，RESTful API 是个什么东西呢？

不妨我们先看下平常的网页是怎么回事。

我们平时浏览的网站，一般分为前端和后端。我们用浏览器观看页面的内容就是前端的工作。

前端采用 Html + CSS + JavaScript 技术来呈现页面内容以及页面效果。

后端主要负责维护数据库并返回前端请求数据库的数据。

如果我们有个需求，不需要那么华丽、炫酷的页面，只需要后端返回的数据。我们把这样的网络请求称为 RESTful API。再者，REST 描述的是在网络中 Client（PC 浏览器、手机 APP 等） 和 Server的一种交互形式；REST本身不实用，实用的是 RESTful API（REST 风格的网络接口）。

后端已经比较成熟的 Web 框架，我们没有必要重复造轮子。

Python Web 主流框架有 Flask、Django、Tornado等

- Flask
Flask 是一个使用 Python 编写的轻量级 Web 应用框架。它基于 Werkzeug WSGI 工具箱和 Jinja2 模板引擎。

Flask 学习成本比较低，花很少的时间成本就能开发出一个简单的博客网站。

如果你时间比较充裕，又想学习 Web 开发。可以学习 Flask ，再以 Flask 做跳板学习其他 Web 框架。

- Django
Django 是以 Python 编写的高级，MVC 风格的开源库。 Django 也被称为“完美主义者的最后框架”。

它最初是为新闻网站设计的，并且允许开发人员编写数据库驱动 Web 应用程序。它算是一个全能型框架。

它内置了很多模块，能快速解决大量 Web 痛点问题。

另外再加上云平台的支持，这使Django 成为 Web 开发者最受欢迎的选择。

大名鼎鼎的 Instagram 网站就是基于 Django 开发的。

- Tornado
Tornado 是传说中性能高高的框架。

它支持异步处理的功能，这是它的优势。因为其他框架不具备该功能。

但 Tornado 也有致命缺点，那就是扩展库资源比较少。

Tornado 除了提供了网站基本需要使用的模块外，剩下的则需要开发者自己进行扩展。

所以，综合以上几点，我就决定深入学习 Django。朋友们，敬请期待我的分享吧。
