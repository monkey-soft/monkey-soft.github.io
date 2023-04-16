---
layout: post
title:  "利用 Github+Jeklly 搭建个人博客网站"
date:   2019-11-10 16:35:28
urlname: 111
cover: https://img.jikehou.cn/cover/2019-11-10.jpg
categories: [建站]
tags: [建站]
keywords: [博客, Jekyll, Github, SEO 优化]
description: 利用 Github+Jeklly 搭建个人博客网站
---

在上篇文章[《个人博客如何选型？》](https://jikehou.cn/posts/how-to-choose-blog-style.html)中讲到，可以利用 Github Pages 来搭建个人博客网站，本文主要讲解其中的各种细节。
<!-- more -->
## 1.评估 Github Pages 方案
前面说到几种博客方案，我都玩过，可以说是各有各的特点与优势。

在你考虑选择是否 Github Pages 方案之前，可以先了解其优缺点，然后根据自己情况加一判定。

- 优点：

1. 完全免费。因为它本质上是一个 Github 仓库，只不过 Github 官方提供网页寄存服务。
2. 简单省心。无须自己购买云服务，也不需要关系环境搭建、系统维护等，我们只需专注写作。
3. 带宽够用。在使用的时候项目和网站的大小不要超过 1GB，也不要过于频繁的更新网站的内容（每小时不超过 10 个版本），每个月的也要注意带宽使用上限为 100GB。这些对于个人网站其实是够用。

- 缺点：

1. 它是静态网站，无法支持数据库，涉及一些数据存储的操作需要自己找其他解决方案。例如页面浏览的阅读人数统计、点赞数等。
2. 访问速度比较慢。我们的网站是寄托在 Github 网站上，Github 服务器节点是在美国，所以访问速度没有国内主机快。

以上是我对于 Github Pages 方案的理解。如果你已经确认选择 Github Pages 方案，继续跟着猴哥的思路，一步步搭建属于自己的博客。

## 2.创建简单页面

首先需要注册一个 GitHub 账号，然后到个人主界面里面，创建一个新的 Repository。

![创建 Repository](https://img.jikehou.cn/img/155_1.jpg)

进入创建新仓库页面后，在 Repository name 的位置填写域名，格式是 `username.GitHub.io`。

我的 Github 的 id 是 monkey-soft，所以域名如下图所示。

![域名](https://img.jikehou.cn/img/155_2.jpg)

创建成功后，点击 `settings` 栏目。

![settings 栏目](https://img.jikehou.cn/img/155_3.jpg)

接着找到 GitHub Pages，然后随便选择 Github 提供的默认主题。

![默认主题](https://img.jikehou.cn/img/155_4.jpg)

![默认主题](https://img.jikehou.cn/img/155_5.jpg)

最后，我们通过浏览器访问仓库的域名（monkey.github.io）就能看到系统创建的默认网页。

## 3.Jekyll 主题选择

上文讲到选择默认的主题，这里都是 Jekyll 主题。

那什么是 Jeklly呢？

它是一个简单静态站点生成器，能将纯文本的(一般是 Markdown 文件)转化为静态页面。

有趣的是，Jeklly 是由 GitHub 的联合创始人 Tom Preston-Werner 基于 Ruby 语言编写的。

因此，GitHub 官方默认采用 Jeklly 来生成网页内容。

原生的 Jeklly 主题过于简单，如果自己想进行改造，工作量还蛮大的。

通常比较方便的做法是利用其他作者制作的主题。猴哥找到三个 Jeklly 主题网站，分别是：

- http://jekyllthemes.org
- https://jekyllthemes.io
- http://themes.jekyllrc.org

你可以根据个人喜好选择适合的主题。我找到一款名为 MatJek 的主题，将主题压缩包下载到本地。

![下载主题](https://img.jikehou.cn/img/155_6.jpg)

主题下载之后，我们需要把主题文件同步到我们的网站，也就是远程的 Github 仓库。

如果你熟悉 Git 用法，可以使用 clone 命令将 Github 仓库下载到本地。

如果你没有任何 Git 的基础，也不想进行一些繁琐的配置，那么推荐使用桌面客户端的形式进行管理。

我们需要下载 `GitHub 桌面客户端`，然后登录自己 Github 账号，接着将仓库拉取到本地。

注意的是，存储路径不能有中文。

![将仓库拉取到本地](https://img.jikehou.cn/img/155_7.jpg)

找到仓库存放的文件夹，将之前所有文件全部删除，把刚才下载的主题文件复制到当前文件夹中。

![当前文件夹](https://img.jikehou.cn/img/155_8.jpg)

我们还需要修改配置文件。`_config.yml` 是 Jekyll 的全局配置文件。里面记录着网站的名字，网站的域名，网站的链接格式等等。

我对于原来的 MatJek 主题进行一些定制化需求，整合起来更像是一个博客网站。

这里我以自己修改主题的配置文件为例。

如果你使用其他主题，根据作者的要求，修改 `_config.yml` 的内容即可。

![更换主题](https://img.jikehou.cn/img/155_9.jpg)

最后一步，我们将刚才修改的内容同步到 Github 远程仓库。

![](https://img.jikehou.cn/img/155_10.jpg)

完成以上操作，我们可以打开浏览器，输入我们仓库地址访问我们的网站。

![访问我们的网站](https://img.jikehou.cn/img/155_11.jpg)

该主题是猴哥基于 MatJek 主题进行修改，同时也修复几个缺陷。

如果你想获取猴哥同款主题，在公众号后台回复『主题』即可获取。

## 4.Jekyll 本地环境搭建

如果我们想对主题界面进行修改，每次预览界面需要将文件同步到 Github 仓库，这样操作不太方便。

因此，我们可以本地搭建个 Jeklly 环境，方便我们调试。

因为 Jeklly 是基于 Ruby 语言编写的，所以我们需要安装 Ruby 环境。

我以 Windows 环境下安装为例，Mac 环境比较简单，可以执行搜索搞定。

首先，我们到 Ruby 下载安装包，最好下载带有包管理工具 devkit，方便后续安装各种组件。

安装 Ruby 之后，会弹出提示安装 MSYS。MSYS 是 Windows 上模拟 GUN 环境的组件。

这里选择`选项 3 `进行安装。

![进行安装](https://img.jikehou.cn/img/155_12.jpg)

进入到本地项目文件中，启动终端，依次执行以下命令。

![依次执行命令](https://img.jikehou.cn/img/155_13.jpg)

```
~ $ gem install jekyll bundler
~ $ bundle install
~ $ bundle exec jekyll serve  # 启动本地服务器
```

然后打开浏览器，访问 http://localhost:4000 就能预览本地网站的界面效果。

## 5.SEO 优化

该主题已经集成用于 SEO 优化的 jekyll-seo-tag 插件，我们简单修改` _config.yml` 里面的这几个字段。

- title（博客主题）
- subtitle（博客副标题）
- description（网站描述，尽量增加跟网站内容的关键字）

接着是优化网站文章的 URL 链接。默认 Jeklly 的 URL 的 Path 路径是 `/:year/:month/:day/:title`，显示效果如`/2019/11/12/我的第一篇文章.html`。这种固定链接不太理想。

猴哥总结 URL 地址的 SEO 优化三个原则：

### 1.日期需要出现在固定链接中。
这基于两个方面的考虑。一是如果数字出现在固定链接里面，等于提醒搜索引擎，这是很旧的内容了，没必要再爬一遍了。另外一个原因是，假如你要修改文章的日期重新发布的话，链接地址就变了，也就是意味着你的反向链接，PR 等等都没有了。

### 2.链接的层次不要太深
默认的固定链接是` /年/月/日/文章名`。这种层次过深，不方便搜索引擎爬虫的抓取，对搜索引擎的收录不太友好。

### 3.链接中不要出现中文
虽然现在的搜索引擎已经能识别URL地址里面的中文字符，
但无论是从美观上，以及中文字符会被转义的角度上看，都是非常差的。

猴哥推荐两种固定链接方案。一种是 `/postname/`，基于文章的英文单词翻译；另一种是 `/post_id/`，基于文章发布的 ID 号。

因为是静态网站，所以必须按照 Jeklly 的规范来设置 URL，第一种方式也就被排除，只能选择第二种方案。Jeklly 提供的路径变量只有几个，不够灵活。最后我花费九牛二虎之力，翻阅 Jeklly 中文网站，找到一个伪 postid 的方案。Path 路径设置为 `/:short_year:i_month:i_day.html`, 即利用日期的部分字段来拼凑。

这里我进行讲解下。比如一篇文章于 2017-06-25 发布的，Jeklly 会将年前面两位去掉；月份如果是10月之前去掉数字 0，没有则直接保留月份；日跟月份一样会去掉数字 0；因此，最后的  Path 路径是 17625.html。

## 6.发布文章
网站主题搭建工作完成之后，我们就能往博客上填充内容。文章一般是用 Markdown 语法编写的，存放在 `_posts` 文件夹中。

![_posts文件夹](https://img.jikehou.cn/img/155_14.jpg)

文件的命名规则是：`年-月-日-文章标题.md`。

![文件的命名规则](https://img.jikehou.cn/img/155_15.jpg)

在 md 文件中，必须带上头部信息才能被识别出来，其中信息有文章标题、编写时间、分类、标签等。

![头部信息](https://img.jikehou.cn/img/155_16.jpg)

我的设想是在首先展示文章时会显示封面图片，我在文件中创建一个名为 img 文件夹来存放封面图片，图片命名须方式是以日期的形式。

当一切工作完成就绪，我们就可以使用 Github 客户端将内容推送到远程仓库。

剧透一下，下篇分享 Github Pages 结合 Hexo 搭建博客网站方案。


