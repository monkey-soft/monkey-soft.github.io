---
layout: post
title:  "Django 使用会话(sessions)功能"
date:   2018-05-07 22:49:58
urlname: 52
cover: https://img.jikehou.cn/cover/2018-05-07.jpg
categories: [从零学Django]
tags: [Python, Web, Django]
keywords: [Python, web, Django, session]
---
淘宝、天猫、京东等电商网站的出现，让我们足不出户就能购物。

在这些网站中，都有一个“购物车”的功能。当我们在不同商品页面将商品加入购物车，然后关闭浏览器。

等下次浏览该网站时，我们会依然发现购物车的商品还在。

这是怎么实现的了？类似这种场景，一般都是采用 Cookie + Session 方式来实现。
<!-- more -->
## 1.Cookie 机制
HTTP 协议是**无状态**的。

所以服务器无法根据 HTTP 协议来辨别多个 HTTP 请求来自哪个用户。

在实际场景中，服务器经常需要追踪客户端的状态。为了解决这个问题， Cookie 技术应运而生。

cookie 一开始是服务器产生的一段随机字符串。

它的主要内容包括：名字，值，过期时间，路径与域等信息。

然后服务器将其发送给客户端。

在后续的请求中，cookie 会附在请求资源的 HTTP 请求头上，发送给服务器。

## 2.Session 机制
如果不涉及用户登录等敏感信息时，Cookie 能够满足大部分的场景需求。

**而客户端（如浏览器）会将 Cookie 是保存在硬盘中**。

如果用户登录敏感信息保存到 cookie 中，会存在安全性问题。

因为当 HTTP 请求被黑客拦截，然后劫持 cookie 信息。

黑客就可以凭借该 cookie 登录对应的网站。

Session 的出现很好地解决的这个问题。

Session 机制是一个服务器端的机制。

**它会将信息保存服务器端，跟客户端通信只需要一个随机的字符串 session_id**。

如果客户端没有禁止 Cookie 功能，session_id 通常是保存在 Cookie中 的。

如果 Cookie 被禁用，它则可能通过为 url 加上query string 来添加 session_id。

## 3.在 Django 中的应用
Django 为我们提供了一个通用的 Session 框架。

使用 Django 2.X 版本创建新项目的时，Django 默认会帮我们启用该功能。

Django 默认 Session 数据保存到数据库中，可以在 settings.py 中看到配置信息项目。
```python
INSTALLED_APPS = [
    # 启用 sessions 应用
    'django.contrib.sessions',
]

MIDDLEWARE = [
    # 启用 Session 中间层
    'django.contrib.sessions.middleware.SessionMiddleware',
]
```
然后我们在将自带组件的模型同步到数据库中。

如果你还不熟悉这块内容，可以阅读[《Django 学习笔记之模型（上）》](https://jikehou.cn/posts/django-learning-notes-models-above.html)这篇文章。

我们之后会看到数据库中有个 `django_session` 表：

![看到数据库中有个 django_session 表](https://img.jikehou.cn/img/82_1.png)

除了上述的基于数据库的会话，Django 还提供另外三种方法：

### 1）保存到缓存中
如果你的场景需要快速存储会话，可以选择该方案。

使用之前，需要配置下 Django 的缓存框架。在 settings.py 中增加 **SESSION_ENGINE** 配置。

这其中也是有两种保存数据的方案，具体配置如下：

#### 方案一

```python
SESSION_ENGINE = 'django.contrib.sessions.backends.cache'
```

这种配置方案 Django 只是简单保存会话。同时，这种方案持久性不好。因为当缓存数据存满时将清除部分数据，或者遇到缓存服务器重启时数据将丢失。

#### 方案二

```python
SESSION_ENGINE = 'django.contrib.sessions.backends.cached_db'
```

这种方案既保证快速存储会话数据，又保证数据持久性。

因为该使用方案， Session 在保存到缓存的同时还会被保存到数据库中，当 Django 在缓存中找不到Session 时，会从数据库中找到。

因此，这种方案的性能开销会比方案一大。

**如果我们在工程中同时配置了数据库会话和缓存会话，Django 默认优秀选择缓存会话。**

### 2）保存到文件中

这种方案是保存数据到本地磁盘中。因为磁盘的 I/O 瓶颈问题，导致这种方案存储数据效率不是很高。

如果要使用这种方案，在 settings.py 中增加 **SESSION_ENGINE** 配置。具体配置如下：

```python
SESSION_ENGINE = 'django.contrib.sessions.backends.file'
# 可选配置
SESSION_FILE_PATH = '/monkey/www/'
```

SESSION_FILE_PATH 默认使用 `tempfile.gettempdir()` 方法的返回值，就像 `/tmp目录`。

如果你想更新文件的保存路径，可以手动指定。

另外需确保你的文件存储目录，以及 Web 服务器对该目录具有读写权限。

### 3）保存到 cookie 中

这种方案将数据保存到 cookie 中。这种方案适用于对数据保密性不严格的场景。

如果要使用这种方案，在 settings.py 中增加 `SESSION_ENGINE` 配置。具体配置如下：

```python
SESSION_ENGINE = 'jango.contrib.sessions.backends.signed_cookies'
# 建议配置，阻止 javascript 对会话数据的访问，提高安全性。
SESSION_COOKIE_HTTPONLY= True
```

