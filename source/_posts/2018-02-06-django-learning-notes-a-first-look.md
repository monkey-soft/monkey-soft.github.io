---
layout: post
title:  "Django 学习笔记之初识"
date:   2018-02-06 12:52:36
urlname: 36
cover: https://img.jikehou.cn/cover/2018-02-06.jpg
categories: [从零学Django]
tags: [Python, Web, Django]
keywords: [Python, web, Django]
---
上篇文章讲述 Django 环境搭建， 在文章最后部分还有 6 篇 Django 简单入门的文章。

后来我自己以一个新手的角度来阅读文章，发现前面三篇文章能被够消化吸收。

但是后三篇文章理解起来可能会有点费劲，可能是我漏写了很多细节。

因此，本文先将前三盘文章的内容做一些补充说明，降低学习成本。
<!-- more -->
## 1.MVC 与 MTV
在 Web 服务器开发领域，MVC 模式可以算是家喻户晓。

有些书籍或者文章说 Django 是一个 MVC 开发框架，另一些文章或者博客则说 Django 是 MTV 模式。

那么 MTV 模式是什么？

Django 又究竟是哪种模式？

Django 是一个遵循 MVC 开发模式的框架 。

我们先看下 MVC 的数据流向，了解 MVC 的工作流程。

![MVC 的工作流程](https://img.jikehou.cn/img/62_1.png)

M 是 Model 的意思 ，它是一个抽象层，用来构建和操作 Web 应用中的数据。同时，Model 层跟数据库打交道的层次，执行数据库数据的增删改查操作。

在 Django 项目中，Model 层逻辑是体现在 **models.py** 中，**models.py**中定义的各种类代表数据模型 model 。每个 model 是对应数据库中唯一的一张表，每个 model 中的字段也对应表中的字段。

V 指的是 View  层。在 Django 项目中，**templates** 文件夹中各个模版文件代表视图（View），负责数据内容的显示。**templates** 文件夹中文件其实就是 HTML、CSS、Javascript 文件。但在 HTML 中使用一些 Django 中特定的特殊语法，就可以实现动态内容插入，从而实现动态页面。

C 全称是 Controller 。它通常是负责从视图读取数据，控制用户输入，并向模型发送数据。在 Django 项目中，**urls.py** （文件路由）中定义的各种 url 访问入口 和 **view.py** 中定义的各种处理函数（被称为 Django 视图函数）代表控制器（Controller ）。**urls.py** 接受用户在浏览器中输入不同 url 地址的请求，然后分发给 view.py 。**view.py** 再根据文件中对应的函数与数据模型和视图交互，响应用户的请求。即将数据填充到模板（templates）中，呈现给用户。

![图片来源于网络](https://img.jikehou.cn/img/62_2.png)

在实际开发过程中，开发者主要操作对象是 models.py、view.py、templates 文件夹中各个模版文件。这就弱化 C 层的概念， 更加注重关注的是模型（Model）、模板(Template)和视图（Views），所以 Django 也被称为 MTV 框架 。

## 2.Django 工作流程
了解 Django 的模式，我们来了解 Django 程序是处理一个 HTTP 请求的流程。

![图片来源于网络](https://img.jikehou.cn/img/62_3.png)

图中显示 Django 程度接受到一个 HTTP 请求到返回请求内容的过程。各个路径的含义如下：
1. 用户使用浏览器浏览网页，浏览器向 Web 服务器发起 HTTP 请求。
2. Web服务器（比如，Nginx）把这个请求转交到一个WSGI（比如，uWSGI），或者直接地文件系统能够取出一个文件（比如返回一个视频文件）。
3. 不像 web 服务器那样，WSGI服务器可以直接运行Python应用。请求生成一个被称为 environ 的 Ptyhon 字典。而且可以选择传递过去几个中间件的层，最终达到 Django 应用。
4. Django 根据请求的路径，URLconf 将请求分配对应的视图文件。这个请求被封装到 HttpRequest 中。URLconf 可以理解为 URL 以及该 URL所调用的视图函数之间的映射表，通常是记录到 urls.py 中。
5. 被选择的那个视图(Views.py 中的类)会根据页面的需求执行一些操作。例如通过模型（Model）与数据库进行通信；使用模板渲染 HTML或者任何格式化过的响应；访问页面出错，抛出一个异常等。在处理过程中，视图处理的对象主要是 HttpResponse。
6. 当 HttpResponse 对象离开 Django 后，被压缩成二进制流传输给浏览器（HTTP 请求的传输的内容是二进制数据）。
7. 浏览器收到 HTTP 的响应头，呈现给用户。

