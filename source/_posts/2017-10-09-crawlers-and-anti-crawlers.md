---
layout: post
title:  "爬虫与反爬虫的博弈"
date:   2017-10-09 17:23:49
urlname: 21
cover: https://img.jikehou.cn/cover/2017-10-09.jpg
categories: [Python爬虫入门]
tags: [Python, 网络爬虫, 反爬虫]
keywords: [Python, 网络爬虫, 反爬虫]
---
今天猴哥给大家说说爬虫与反爬虫的博弈。
<!-- more -->
## 1.前言
近来这两三个月，我陆续将自己学到的爬虫技术分享出来。

以标准网络库 urllib 的用法起笔，接着介绍各种内容提供工具，再到后续的 scrapy 爬虫框架系列。

我的爬虫分享之旅已经接近尾声了。

本文就来聊聊如何防止爬虫被 ban 以及如何限制爬虫。

## 2.介绍
我们编写的爬虫在爬取网站的时候，要遵守 robots 协议，爬取数据做到“盗亦有道”。

在爬取数据的过程中，不要对网站的服务器造成压力。

尽管我们做到这么人性化。对于网络维护者来说，他们还是很反感爬虫的。

因为爬虫的肆意横行意味着自己的网站资料泄露，甚至是自己刻意隐藏在网站的隐私的内容也会泄露。

所以，网站维护者会运用各种方法来拦截爬虫。

## 3.攻防战
### 3-1.场景一

`防`：检测请求头中的字段，比如：User-Agent、referer等字段。

`攻`：只要在 http 请求的 headers 中带上对于的字段即可。下图中的七个字段被大多数浏览器用来初始化所有网络请求。建议将以下所有字段都带上。

![建议将以下所有字段都带上](https://img.jikehou.cn/img/44_1.png)

### 3-2.场景二

`防`：后台对访问的 IP 进行统计，如果单个 IP 访问超过设定的阈值，给予封锁。虽然这种方法效果还不错， 但是其实有两个缺陷。 一个是非常容易误伤普通用户， 另一个就是 IP 其实不值钱， 各种代理网站都有出售大量的 IP 代理地址。 所以建议加大频率周期,每小时或每天超过一定次数屏蔽 IP 一段时间（不提示时间）。

`攻`：针对这种情况，可通过使用代理服务器解决。同时，爬虫设置下载延迟，每隔几次请求，切换一下所用代理的IP地址。

### 3-3.场景三

`防`：后台对访问进行统计， 如果单个 userAgent 访问超过阈值， 予以封锁。这种方法拦截爬虫效果非常明显，但是杀伤力过大，误伤普通用户概率非常高。所以要慎重使用。
`攻`：收集大量浏览器的 userAgent 即可。

### 3-4.场景四

`防`：网站对访问有频率限制，还设置验证码。增加验证码是一个既古老又相当有效果的方法。能够让很多爬虫望风而逃。而且现在的验证码的干扰线, 噪点都比较多，甚至还出现了人类肉眼都难以辨别的验证码（12306 购票网站）。
`攻`：python+tesseract 验证码识别库模拟训练，或使用类似 tor 匿名中间件（广度遍历IP）

### 3-5.场景五

`防`：网站页面是动态页面，采用 Ajax 异步加载数据方式来呈现数据。这种方法其实能够对爬虫造成了绝大的麻烦。

`攻`：首先用 Firebug 或者 HttpFox 对网络请求进行分析。如果能够找到 ajax 请求，也能分析出具体的参数和响应的具体含义。则直接模拟相应的http请求，即可从响应中得到对应的数据。这种情况，跟普通的请求没有什么区别。

能够直接模拟ajax请求获取数据固然是极好的，但是有些网站把 ajax 请求的所有参数全部加密了。

我们根本没办法构造自己所需要的数据的请求，请看场景六。

### 3-6.场景六

`防`：基于 JavaScript 的反爬虫手段，主要是在响应数据页面之前，先返回一段带有JavaScript 代码的页面，用于验证访问者有无 JavaScript 的执行环境，以确定使用的是不是浏览器。例如淘宝、快代理这样的网站。

这种反爬虫方法。通常情况下，这段JS代码执行后，会发送一个带参数key的请求，后台通过判断key的值来决定是响应真实的页面，还是响应伪造或错误的页面。

因为key参数是动态生成的，每次都不一样，难以分析出其生成方法，使得无法构造对应的http请求。

`攻`：采用 selenium+phantomJS 框架的方式进行爬取。调用浏览器内核，并利用phantomJS 执行 js 来模拟人为操作以及触发页面中的js脚本。从填写表单到点击按钮再到滚动页面，全部都可以模拟，不考虑具体的请求和响应过程，只是完完整整的把人浏览页面获取数据的过程模拟一遍。 
