---
layout: post
title:  "学会运用爬虫框架 Scrapy (四)  —— 高效下载图片"
date:   2017-09-26 23:03:35
urlname: 19
cover: https://img.jikehou.cn/cover/2017-09-26.jpg
categories: [Python爬虫入门]
tags: [Python, 网络爬虫, Scrapy, 爬虫实战,]
keywords: [Python, 网络爬虫, Scrapy, 爬虫实战, 下载图片]
---
爬虫程序爬取的目标通常不仅仅是文字资源，经常也会爬取图片资源。这就涉及如何高效下载图片的问题。

这里高效下载指的是既能把图片完整下载到本地又不会对网站服务器造成压力。

也许你会这么做，在 pipeline 中自己实现下载图片逻辑。

但 Scrapy 提供了图片管道`ImagesPipeline`，方便我们操作下载图片。
<!-- more -->
## 1.为什么要选用 ImagesPipeline ？
`ImagesPipeline ` 具有以下特点：
- 将所有下载的图片转换成通用的格式（JPG）和模式（RGB）
- 避免重新下载最近已经下载过的图片
- 缩略图生成
- 检测图像的宽/高，确保它们满足最小限制


## 2.具体实现
### 2-1.定义字段
在 item.py 文件中定义我们两个字段`image_urls` 和` images_path`
```python
import scrapy

class PicsDownloadItem(scrapy.Item):
    # define the fields for your item here like:
    # name = scrapy.Field()

    image_urls = scrapy.Field()  # 图片的下载地址， 该字段是存储图片的列表
    image_path = scrapy.Field()  # 图片本地存储路径(相对路径)
```

### 2-2.编写 spider
我以爬取 freebuf 首页部分图片为例子讲解。具体代码如下：
```python
import scrapy

from pics_download.items import PicsDownloadItem

class freebuf_pic_spider(scrapy.Spider):

    name = 'freebuf'

    allowed_domains = ['freebuf.com']

    start_urls = [
        'http://www.freebuf.com/'
    ]

    def parse(self, response):
        self.log(response.headers)
        # 获取 freebuf 首页所有的图片, 以列表形式保存到 image_urls 字段中。
        piclist = response.xpath("//div[@class='news-img']/a/img/@src").extract()
        if piclist:
            item = PicsDownloadItem()
            item['image_urls'] =  piclist  
            yield item
```

### 2-3.实现 Pipeline
我新建一个名为`PicsDownloadPipeline`的类。

需要注意一点的是： Scrapy 默认生成的类是继承`Object`， 要将该类修改为继承`ImagesPipeline`。

然后实现`get_media_requests`和`item_completed`这两个函数。

- `get_media_requests(item, info)`

ImagePipeline 根据 image_urls 中指定的 url 进行爬取，可以通过 get_media_requests 为每个 url 生成一个 Request。具体实现如下：
```python
def get_media_requests(self, item, info):
    for image_url in item['image_urls']:
        yield scrapy.Request(image_url)
```

- `item_completed(self, results, item, info)`

当一个单独项目中的所有图片请求完成时，该方法会被调用。处理结果会以二元组的方式返回给 item_completed() 函数。

这个二元组定义如下：(success, image_info_or_failure)

其中，第一个元素表示图片是否下载成功；第二个元素是一个字典，包含三个属性：
1)  url - 图片下载的url。这是从 get_media_requests() 方法返回请求的url。
2)  path - 图片存储的路径（类似 IMAGES_STORE）
3)  checksum - 图片内容的 MD5 hash

具体实现如下：
```python
def item_completed(self, results, item, info):
    # 将下载的图片路径（传入到results中）存储到 image_paths 项目组中，如果其中没有图片，我们将丢弃项目:
    image_path = [x['path'] for ok, x in results if ok]
    if not image_path:
        raise DropItem("Item contains no images")
    item['image_path'] = image_path
    return item
```

综合起来，PicsDownloadPipeline 的实现下载图片逻辑的代码如下：
```python
import scrapy
from scrapy.exceptions import DropItem
from scrapy.pipelines.images import ImagesPipeline

class PicsDownloadPipeline(ImagesPipeline):

    def get_media_requests(self, item, info):
        for image_url in item['image_urls']:
            yield scrapy.Request(image_url)

    def item_completed(self, results, item, info):
        # 将下载的图片路径（传入到results中）存储到 image_paths 项目组中，如果其中没有图片，我们将丢弃项目:
        image_path = [x['path'] for ok, x in results if ok]
        if not image_path:
            raise DropItem("Item contains no images")
        item['image_path'] = image_path
        return item
```

### 2-4.配置设置
在 setting.py 配置存放图片的路径以及自定义下载的图片管道。
```python
# 设置存放图片的路径
IMAGES_STORE = 'D:\\freebuf'

# 配置自定义下载的图片管道， 默认是被注释的
ITEM_PIPELINES = {
    # 第二行的填写规则
    #  yourproject.middlewares(文件名).middleware类
   'pics_download.pipelines.PicsDownloadPipeline': 300,  # pics_download 是你项目的名称
}
```

### 2-5.运行程序
在 Scrapy 项目的根目录下，执行以下命令：
```python
scrapy crawl freebuf  # freebuf 是我们在 spider 定义的 name 属性
```

如果你使用的 Python 版本是 3.x 的，可能会报出以下的错误。
```python
  File "c:\program files (x86)\python36-32\lib\site-packages\scrapy\pipelines\images.py", line 15, in <module>
    from PIL import Image
ModuleNotFoundError: No module named 'PIL'
```
这是因为 Scrapy  框架用到这个`Python Imaging Library (PIL)`图片加载库，但是这个库只支持 2.x 版本，所以会运行出错。

对于使用 Python 3.x 版本的我们，难道就束手无策？

Scrapy 的开发者建议我们使用更好的图片加载库`Pillow `。

为什么说更好呢？一方面是兼容了 PIL，另一方面在该库支持生成缩略图。

因此，我们安装 Pillow 就能解决运行报错的问题。具体安装 `Pillow `命令如下：
```python
pip install pillow 
# 如果出现因下载失败导致安装不上的情况，可以先启动 ss 再执行安装命令
# 或者在终端中使用代理
pip --proxy http://代理ip:端口 install pillow 
```

安装之后，重新运行爬虫程序。Scrapy 会运行结果中显示我们定义的`image_urls` 和` images_path`字段。

![重新运行爬虫程序，运行结果](https://img.jikehou.cn/img/42_1.png)

### 2-6.运行结果
我们会发现在 D 盘有个名为`freebuf`的文件夹。在该文件夹中有个`full`文件夹，里面存放我们刚才爬取到的图片。

![运行结果](https://img.jikehou.cn/img/42_2.png)

如果有在 setting.py 文件中设置生成缩略图。
```python
IMAGES_THUMBS = {
    'small': (50, 50),   # (宽， 高)
    'big': (270, 270),
}
```

那么到时候，与`full`同级的目录下会多出个`thumbs`文件夹。里面会有两个文件夹`small`和`big`，分别对应小分辨率的图片和大分辨率的图片。


## 3.优化
### 3-1.避免重复下载
在 setting.py 中新增以下配置可以避免下载最近已经下载的图片。
```python
# 90天的图片失效期限
IMAGES_EXPIRES = 90
```
设置该字段，对于已经完成爬取的网站，重新运行爬虫程序。爬虫程序不会重新下载新的图片资源。


### 3-2.自动限速（AutoTrottle）
下载图片是比较消耗服务器的资源以及流量。如果图片资源比较大，爬虫程序一直在下载图片。这会对目标网站造成一定的影响。同时，爬虫有可能遭到封杀的情况。

因此，我们有必要对爬虫程序做爬取限速处理。Scrapy 已经为我们提供了`AutoTrottle`功能。

只要在 setting.py 中开启`AutoTrottle`功能并配置限速算法即可。我采用默认的配置，具体配置如下： 
```python
# 启用AutoThrottle扩展
AUTOTHROTTLE_ENABLED = True
# 初始下载延迟(单位:秒)
AUTOTHROTTLE_START_DELAY = 5
# 在高延迟情况下最大的下载延迟(单位秒)
AUTOTHROTTLE_MAX_DELAY = 60
# 设置 Scrapy应该与远程网站并行发送的平均请求数, 目前是以1个并发请求数
AUTOTHROTTLE_TARGET_CONCURRENCY = 1.0
# 启用AutoThrottle调试模式
#AUTOTHROTTLE_DEBUG = False
```

值得注意的是，启用AutoThrottle扩展时，仍然受到`DOWNLOAD_DELAY`（下载延迟）和`CONCURRENT_REQUESTS_PER_DOMAIN`（对单个网站进行并发请求的最大值）以及`CONCURRENT_REQUESTS_PER_IP`（对单个IP进行并发请求的最大值）的约束。

## 4.源码

如果你想获取项目的源码

{% btn 'https://github.com/monkey-soft/scrapy_demo', 源码下载, far fa-hand-point-right,blue larger %}