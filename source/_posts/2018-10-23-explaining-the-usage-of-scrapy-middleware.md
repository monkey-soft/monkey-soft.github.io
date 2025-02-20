---
layout: post
title:  "详解 Scrapy 中间键的用法"
date:   2018-10-23 22:35:29
urlname: 82
cover: https://img.jikehou.cn/cover/2018-10-23.jpg
categories: [Python爬虫入门]
tags: [Python, Scrapy]
keywords: [Python, Scrapy, 中间键]
description: 详解 Scrapy 中间键的用法
---
Scrapy 爬虫框架的出现，确实能让我们更加专注于数据抓取。

同时，我们借助 Scrapy 框架来爬取整个站点数据也显得更加容易。

虽然 Scarpy 负责 url 调度、网络请求、页面数据下载等工作，但是它的扩展性很高，其中就支持自定义中间件(Middleware)。

本文主要讲解中间件(Middleware)的用法。
<!-- more -->
## 1.什么是中间件
中间件的运用比较广泛，如果直接从定义的角度去理解中间件会有点乱，我以分布式系统为例子进行说明。

在上篇文章，我讲到目前后台服务架构基本都是往分布式发展。其实分布式系统也算是一个中间件。

那什么是分布式系统？

分布式系统是由一组通过网络进行通信、为了完成共同的任务而协调工作的计算机节点组成的系统。

我们从下图中可得知，分布式系统是介于操作系统和用户应用之间的软件。

![分布式系统是介于操作系统和用户应用之间的软件](https://img.jikehou.cn/img/20181023_1.png)

那么不妨我们进一步拓展下思维去理解中间件。可知，中间件(middleware)是基础软件的一大类，属于可复用软件的范畴。顾名思义，中间件处于操作系统软件与用户的应用软件的中间。

## 2.中间件在 Scrapy 框架中的作用
我们先通过一张图了解下 Scrapy 架构。

![Scrapy 架构](https://img.jikehou.cn/img/20170904_5.png)

我们可以看到 Scrapy 框架是有两个中间件。

一个是 Downloader 中间件，它是 Engine 和 Downloader 的枢纽。

主要负责处理 Downloader 传递给 Engine 的 responses；

另一个是 Spider 中间件，它Spider 中间件是 Engine 和 Spider 的连接桥梁；

它主要是处理 Spider 的输入(responses) 以及输出 item 和 requests 给 Engine；

## 3.实现自己的中间件

在 Scrapy 框架中，Downloader 中间件和 Spider 中间件都是支持自定义扩展。

在实际应用中，我们经常需要对 Downloader 中间件进行制定化。

例如实现一个 User-Agent 中间件给每个 HTTP 请求的头部增加随机筛选的 User-Agent 属性；

或者实现一个代理中间件给每个 HTTP 请求设置随机选择的代理地址。

接下来，让我们学习如何实现 Scrapy 的 Downloader 中间件。

### 3-1.定义中间件

在 Scrapy 项目中，找到 `middlewares.py` 文件，在文件中创建自己的中间件类。

例如，我创建一个代理中间件：
```python
class ProxyMiddleware(object):
```

每个中间件一共有三个方法，分别是：
#### process_request(request,spider)

当每个 request 通过下载中间件时，该方法被调用。

该方法必须返回以下三种中的任意一种：None，返回一个 Response 对象，返回一个 Request 对象或 raise IgnoreRequest。每种返回值的作用是不同的。

None: Scrapy 将会继续处理该 request，执行其他的中间件的相应方法，直到合适的下载器处理函数( download handler )被调用,该 request 被执行(其 response被下载)。

**如果有多个中间件，其他的中间件可以通过返回 Null，然后指定对应的中间件去处理 request**

Request 对象：Scrapy 则停止调用 process_request 方法并重新调度返回的 request。

**简单来说是拒绝该 Request 的 HTTP 请求。**

Response 对象：直接返回结果。

raise IgnoreRequest 异常：抛出异常，然后会被中间件的 process_exception() 方法会被调用。

#### process_response(request, response, spider)

process_response 的返回值也是有三种：Response 对象，Request对象，或者 raise 一个 IgnoreRequest 异常。

如果返回的结果是 Response， 该 response 会被在链中的其他中间件的 process_response() 方法处理。

如果的结果是 Request 对象，则中间件链停止，request 会被重新调度下载。

raise IgnoreRequest 异常: 抛出异常，然后会被中间件的 process_exception() 方法会被调用。。

#### process_exception(request, exception, spider)

当下载处理器(download handler)或 process_request() (下载中间件)抛出异常(包括 IgnoreRequest 异常)时，Scrapy 调用 process_exception()。

process_exception() 的返回结果同样也是有三个: None、Response 对象、Request 对象。

如果其返回 None ，Scrapy 将会继续处理该异常，接着调用已安装的其他中间件的 process_exception() 方法，直到所有中间件都被调用完毕。

如果其返回一个 Response 对象，则其他中间件链的 process_response() 方法被调用，之后 Scrap y将不会调用其他中间件的 process_exception() 方法。

如果其返回一个 Request 对象， 则返回的request将会被重新调用下载。这将停止中间件的 process_exception() 方法执行，就如返回一个 response 的那样。

`如果 HTTP 请求失败，我们可以在这里对 HTTP 请求进行重试。例如我们频繁爬取访问一个网站导致被封 IP，就可以在这里设置增加代理继续访问。`

我们可以不用实现全部方法，只需要根据需求实现对应的方法即可。

例如，我想给每个 HTTP 请求都添加代理地址， 我实现 process_request() 即可。
```Python
class ProxyMiddleware(object):
    # overwrite process request
    def process_request(self, request, spider):
        # 从数据库中随机读取一个代理地址
        proxy_address = proxy_pool.random_select_proxy()
        logging.debug("=====  ProxyMiddleware get a random_proxy:【 {} 】 =====".format(proxy_address))
        request.meta['proxy'] = proxy_address
        return None
```

### 3-2.在 setting.py 启用中间件
我们已经实现了中间件，最后一步需要启用该中间件。

我们将定义的中间件添加到 settings.py 文件。

如果你是重载系统中间件，还需要将系统的中间件的值设置为 None。

我前面定义的代理中间件，是需要对 HTTP 请求做操作。

所以重载了 HttpProxyMiddleware 中间件。
```Python
#  中间件填写规则
#  yourproject.myMiddlewares(文件名).middleware类

# 设置代理
'scrapy.contrib.downloadermiddleware.httpproxy.HttpProxyMiddleware': None,
'scrapydemo.middlewares.ProxyMiddleware': 100,
```
