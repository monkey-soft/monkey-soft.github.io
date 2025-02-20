---
layout: post
title:  "不懂代码也能爬取数据？试试这几个工具"
date:   2020-04-08 18:28:17
urlname: 117
cover: https://img.jikehou.cn/cover/2020-04-08.jpg
categories: [Python爬虫入门]
tags: [数据采集, 网络爬虫]
keywords: [数据采集器, 网络爬虫, 火车头, 八爪鱼, GooSeeker 集搜客, Scrapinghub, WebScraper]
---
前天，有个同学加我微信来咨询我：

**“猴哥，我想抓取近期 5000 条新闻数据，但我是文科生，不会写代码，请问该怎么办？”**

猴哥有问必答，对于这位同学的问题，我给安排上。

先说说获取数据的方式：

一是利用现成的工具，我们只需懂得如何使用工具就能获取数据，不需要关心工具是怎么实现。

打个比方，假如我们在岸上，要去海上某个小岛，岸边有一艘船，我们第一想法是选择坐船过去，而不会想着自己来造一艘船再过去。

第二种是自己针对场景需求做些定制化工具，这就需要有点编程基础。

举个例子，我们还是要到海上某个小岛，同时还要求在 30 分钟内将 1 顿货物送到岛上。
<!-- more -->
因此，前期只是单纯想获取数据，没有什么其他要求的话，优先选择现有工具。

可能是 Python 近来年很火，加上我们会经常看到别人用 Python 来制作网络爬虫抓取数据。

从而有一些同学有这样的误区，想从网络上抓取数据就一定要学 Python，一定要去写代码。

其实不然，猴哥介绍几个能快速获取网上数据的工具。

## 1.Microsoft Excel

你没有看错，就是 Office 三剑客之一的 Excel。

Excel 是一个强大的工具，能抓取数据就是它的功能之一。

我以耳机作为关键字，抓取京东的商品列表。

![抓取京东的商品列表-1](https://img.jikehou.cn/img/20200408_1.jpg)

![抓取京东的商品列表-2](https://img.jikehou.cn/img/20200408_2.jpg)

等待几秒后，Excel 会将页面上所有的文字信息抓取到表格中。

这种方式确实能抓取到数据，但也会引入一些我们不需要的数据。

如果你有更高的需求，可以选择后面几个工具。

## 2.火车头采集器

![火车头采集器](https://img.jikehou.cn/img/20200408_3.jpg)

火车头是爬虫界的老品牌了，是目前使用人数最多的互联网数据抓取、处理、分析，挖掘软件。

它的优势是采集不限网页，不限内容，同时还是分布式采集，效率会高一些。

缺点是对小白用户不是很友好，有一定的知识门槛（了解如网页知识、HTTP 协议等方面知识），还需要花些时间熟悉工具操作。

因为有学习门槛，掌握该工具之后，采集数据上限会很高。有时间和精力的同学可以去折腾折腾。

{% btn 'http://www.locoy.com/', 火车头采集器官网地址, far fa-hand-point-right,blue larger %}

## 3.八爪鱼采集器

![八爪鱼采集器](https://img.jikehou.cn/img/20200408_4.jpg)

八爪鱼采集器是一款非常适合新手的采集器。它具有简单易用的特点，让你能几分钟中就快手上手。八爪鱼提供一些常见抓取网站的模板，使用模板就能快速抓取数据。如果想抓取没有模板的网站，官网也提供非常详细的图文教程和视频教程。

八爪鱼是基于浏览器内核实现可视化抓取数据，所以存在卡顿、采集数据慢的特点。但这瑕不掩瑜，能基本满足新手在短时间抓取数据的场景，比如翻页查询，Ajax 动态加载数据等。

{% btn 'https://www.bazhuayu.com/', 八爪鱼采集器官网地址, far fa-hand-point-right,blue larger %}

## 4.GooSeeker 集搜客

![GooSeeker](https://img.jikehou.cn/img/20200408_5.jpg)

集搜客也是一款容易上手的可视化采集数据工具。同样能抓取动态网页，也支持可以抓取手机网站上的数据，还支持抓取在指数图表上悬浮显示的数据。集搜客是以浏览器插件形式抓取数据。虽然具有前面所述的优点，但缺点也有，无法多线程采集数据，出现浏览器卡顿也在所难免。

{% btn 'https://www.gooseeker.com/', 集搜客官网地址, far fa-hand-point-right,blue larger %}

## 5.Scrapinghub

![Scrapinghub](https://img.jikehou.cn/img/20200408_6.jpg)

如果你想抓取国外的网站数据，可以考虑 Scrapinghub。Scrapinghub 是一个基于Python 的 Scrapy 框架的云爬虫平台。Scrapehub 算是市场上非常复杂和强大的网络抓取平台，提供数据抓取的解决方案商。


{% btn 'https://scrapinghub.com/', Scrapinghub官网地址, far fa-hand-point-right,blue larger %}

## 6.WebScraper

![WebScraper](https://img.jikehou.cn/img/20200408_7.jpg)

WebScraper 是一款优秀国外的浏览器插件。同样也是一款适合新手抓取数据的可视化工具。我们通过简单设置一些抓取规则，剩下的就交给浏览器去工作。

{% btn 'https://webscraper.io/', WebScraper官网地址, far fa-hand-point-right,blue larger %}
