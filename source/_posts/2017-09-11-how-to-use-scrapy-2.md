---
layout: post
title:  "学会运用爬虫框架 Scrapy (二)"
date:   2017-09-11 21:43:47
urlname: 17
cover: https://img.jikehou.cn/cover/2017-09-11.jpg
categories: [Python爬虫入门]
tags: [Python, 网络爬虫, Scrapy, 爬虫实战]
keywords: [Python, 网络爬虫, Scrapy, 爬虫实战]
---
上篇文章介绍了爬虫框架 Scrapy 如何安装，以及其特性、架构、数据流程。

相信大家已经对 Scrapy 有人了初步的认识。

本文是 Scrapy 系列文章的第二篇，主要通过一个实例讲解 scrapy 的用法。
<!-- more -->
## 1.选取目标
网络爬虫，顾名思义是对某个网站或者系列网站，按照一定规则进行爬取信息。

爬取程序的首要工作当然是选定爬取目标。

本次爬取目标选择是`V电影`，网址是`http://www.vmovier.com/`。

爬取内容是[最新推荐]栏目的前15条短视频数据信息。具体信息包括封面、标题、详细说明以及视频播放地址。

![最新推荐](https://img.jikehou.cn/img/20170911_1.png)

## 2.定义 Item
为什么将爬取信息定义清楚呢？

因为接下来 Item 需要用到。在 Item.py 文件中，我们以类的形式以及 Field 对象来声明。

其中 Field 对象其实是一个字典类型，用于保存爬取到的数据。而定义出来的字段，可以简单理解为数据库表中的字段，但是它没有数据类型。

Item 则复制了标准的 dict API，存放以及读取跟字典没有差别。

V电影的 Item，我们可以这样定义：
```python
import scrapy

class ScrapyDemoItem(scrapy.Item):
    # define the fields for your item here like:
    # name = scrapy.Field()
    # 封面
    cover = scrapy.Field()
    # 标题
    title = scrapy.Field()
    # 简述
    dec = scrapy.Field()
    # 播放地址
    playUrl = scrapy.Field()
```

## 3.编写 Spider
Spider 目录是我们爬虫程序爬取网站以及提取信息的模块。

我们首先在目录下新建一个名为 VmoiveSpider 的文件。同时，该类继承`scrapy.Spider`。

这里我们用到的`scrapy.spider.Spider` 是 Scrapy 中最简单的内置 spider。继承 spider 的类需要定义父类中的属性以及实现重要的方法。

### 3-1.`name`

这个属性是非常重要的，所以必须定义它。定义 name 目的是为爬虫程序命名。

因此，还要保持 name 属性是唯一的。它是 String 类型，我们在 VmoiveSpider 可以定义：

```python
name = 'vmoive'
```

### 3-2. `start_urls`

start_urls 是 Url 列表，也是必须被定义。可以把它理解为存放爬虫程序的主入口 url 地址的容器。

### 3-3.`allowed_domains`

可选字段。包含了spider允许爬取的域名(domain)列表(list)。 当 OffsiteMiddleware 启用时， 域名不在列表中的URL不会被跟进。

根据 V 电影的 url 地址，我们可以这样定义：

```python
allowed_domains = ['vmovier.com']
```

### 3-4.`parse(response)`

parser 方法是Scrapy处理下载的response的默认方法。它同样必须被实现。

parse 主要负责处理 response 并返回处理的数据以及跟进的URL。

该方法及其他的Request回调函数必须返回一个包含 Request 及(或) Item 的可迭代的对象。

在 scrapy_demo/sipders/VmoiveSpider 的完整代码如下：

```python
#!/usr/bin/env python
# coding=utf-8

import scrapy
from scrapy_demo.items import ScrapyDemoItem

class VmoiveSpider(scrapy.Spider):
    # name是spider最重要的属性, 它必须被定义。同时它也必须保持唯一
    name = 'vmoive'

    # 可选定义。包含了spider允许爬取的域名(domain)列表(list)
    allowed_domains = ['vmovier.com']

    start_urls = [
        'http://www.vmovier.com/'
    ]

    def parse(self, response):
        self.log('item page url is ==== ' + response.url)

        moivelist = response.xpath("//li[@class='clearfix']")
        for m in moivelist:
            item = ScrapyDemoItem()
            item['cover'] = m.xpath('./a/img/@src')[0].extract()
            item['title'] = m.xpath('./a/@title')[0].extract()
            item['dec'] = m.xpath("./div/div[@class='index-intro']/a/text()")[0].extract()
            print(item)
            yield item
```

## 4.运行程序
在项目目录下打开终端，并执行以下命令。

我们没有`pipelines.py`中将爬取结果进行存储，所以我们使用 scrapy 提供的导出数据命令，将 15 条电影信息导出到名为 items.json 文件中。

其中 vmoive 为刚才在 VmoiveSpider 中定义的 name 属性的值。

```python
scrapy crawl vmoive -o items.json
```

运行的部分结果如下：
```python
{
'cover': 'http://cs.vmoiver.com/Uploads/cover/2017-09-08/59b25a504e4e0_cut.jpeg@600w_400h_1e_1c.jpg',
 'dec': '15年过去了，但我依然有话说',
 'title': '灾难反思纪录短片《“911”之殇》'
 }
```

## 5.深究
在阅读上述代码过程中，大家可能会有两个疑问。

第一，为什么要在 xpath 方法后面添加[0]？ 
第二，为什么要在 [0] 后面添加 extract()方法 ? 

请听我慢慢道来。

1) 添加个[0], 因为 xpath() 返回的结果是列表类型。我以获取标题内容为例子讲解不添加[0]会出现什么问题。那么代码则变为
```python
m.xpath('./a/@title').extract()
```
运行结果会返回一个列表，而不是文本信息。
```python
['灾难反思纪录短片《“911”之殇》']
```

2）这里涉及到内建选择器 Selecter 的知识。extract()方法的作用是串行化并将匹配到的节点返回一个unicode字符串列表。看了定义，是不是更加懵逼了。那就看下运行结果来压压惊。
不加上 extract() 的运行结果如下：
```python
<Selector xpath='./a/@title' data='灾难反思纪录短片《“911”之殇》'>
```

## 6.进阶
上述代码只是在 V电影主页中提取信息，而进入电影详情页面中匹配搜索信息。

因此，我们是获取不到电影的播放地址的。

如何搞定这难题？

我们可以在 parse 方法中做文章。parse() 前文提到它必须返回一个 Reuqest 对象或者 Item。

再者， Request 中就包含 url。换句话说，我们只有获取到电影详情页的 url 地址，并在传递给返回的 Request 对象中。

因此，代码可以这么改进：
```python
#!/usr/bin/env python
# coding=utf-8

import scrapy
from scrapy_demo.items import ScrapyDemoItem

class VmoiveSpider(scrapy.Spider):
    # name是spider最重要的属性, 它必须被定义。同时它也必须保持唯一
    name = 'vmoive'

    # 可选定义。包含了spider允许爬取的域名(domain)列表(list)
    allowed_domains = ['vmovier.com']

    start_urls = [
        'http://www.vmovier.com/'
    ]

    def parse(self, response):
        self.log('item page url is ==== ' + response.url)

        moivelist = response.xpath("//li[@class='clearfix']")

        for m in moivelist:
            item = ScrapyDemoItem()
            item['cover'] = m.xpath('./a/img/@src')[0].extract()
            item['title'] = m.xpath('./a/@title')[0].extract()
            item['dec'] = m.xpath("./div/div[@class='index-intro']/a/text()")[0].extract()
            # print(item)

            # 提取电影详细页面 url 地址 
            urlitem = m.xpath('./a/@href')[0].extract()
            url = response.urljoin(urlitem)
            # 如果你想将上面的 item 字段传递给 parse_moive, 使用 meta 参数
            yield scrapy.Request(url, callback=self.parse_moive, meta={
                'cover':item['cover'],
                'title': item['title'],
                'dec': item['dec'],
            })

    def parse_moive(self, response):
        item = ScrapyDemoItem()
        item['cover'] = response.meta['cover']
        item['title'] = response.meta['title']
        item['dec'] = response.meta['dec']
        item['playUrl'] = response.xpath("//div[@class='p00b204e980']/p/iframe/@src")[0].extract()
        yield item
```

再次运行程序，查看运行结果。
![运行结果](https://img.jikehou.cn/img/20170911_2.png)

## 7.数据持久化
在实际生产中，我们很少把数据导出到 json 文件中。

因为后期维护、数据查询、数据修改都是一件麻烦的事情。我们通常是将数据保存到数据库中。

我们先定义并创建数据库表
```sql
DROP TABLE IF EXISTS vmoive
CREATE TABLE vmoive(
  id INT(6) NOT NULL AUTO_INCREMENT PRIMARY KEY ,
  cover VARCHAR(255),
  title VARCHAR(255),
  mdec VARCHAR(255),
  playUrl VARCHAR(255)
) DEFAULT  CHARSET=utf8;
```

在 settings 文件中增加数据库的配置
```python
# Configure item pipelines  这里默认是注释的
# See http://scrapy.readthedocs.org/en/latest/topics/item-pipeline.html
ITEM_PIPELINES = {
   'scrapy_demo.pipelines.ScrapyDemoPipeline': 300,     # 保存到 mysql 数据库中
}

# Mysql 配置信息
# 根据你的环境修改
MYSQL_HOST = '127.0.0.1'
MYSQL_DBNAME = 'vmoive'     # 数据库名
MYSQL_USER = 'root'         # 数据库用户
MYSQL_PASSWORD = '123456'   # 数据库密码
```

在 scrapy 中，我们要在 pipeline 文件中编写处理数据存储的代码。
```python
# -*- coding: utf-8 -*-
import pymysql
from scrapy_demo import settings

class ScrapyDemoPipeline(object):

    def __init__(self,):
        self.conn = pymysql.connect(
            host        = settings.MYSQL_HOST,
            db          = settings.MYSQL_DBNAME,
            user        = settings.MYSQL_USER,
            passwd      = settings.MYSQL_PASSWORD,
            charset     = 'utf8',  # 编码要加上，否则可能出现中文乱码问题
            use_unicode = False )
        self.cursor = self.conn.cursor()

    # pipeline 默认调用
    def process_item(self, item, spider):
        # 调用插入数据的方法
        self.insertData(item)
        return item

    # 插入数据方法
    def insertData(self, item):
        sql = "insert into vmoive(cover, title, mdec, playUrl) VALUES(%s, %s, %s, %s);"
        params = (item['cover'], item['title'], item['dec'], item['playUrl'])
        self.cursor.execute(sql, params)
        self.conn.commit()
```