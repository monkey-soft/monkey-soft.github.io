---
layout: post
title:  "干将莫邪” —— Xpath 与 lxml 库"
date:   2017-08-07 14:55:51
urlname: 13
cover: https://img.jikehou.cn/cover/2017-08-07.jpg
categories: [Python爬虫入门]
tags: [Python, 网络爬虫, Xpath, lxml]
keywords: [Python, 网络爬虫, Xpath, lxml]
---
前面的文章，我们已经学会正则表达式以及 BeautifulSoup库的用法。

我们领教了正则表达式的便捷，感受 beautifulSoup 的高效。

本文介绍也是内容提取的工具 —— Xpath，它一般和 lxml 库搭配使用。

所以，我称这两者为“干将莫邪”。
<!-- more -->

## 1.Xpath 和 lxml

### 1-1.Xpath

XPath即为XML路径语言，它是一种用来确定XML（标准通用标记语言的子集）文档中某部分位置的语言。XPath 基于 XML 的树状结构，提供在数据结构树中找寻节点的能力。

Xpath 原本是用于选取 XML 文档节点信息。XPath 是于 1999 年 11 月 16 日 成为 W3C 标准。因其既简单方便又容易，所以它逐渐被人说熟知。

### 1-2.lxml 

lxml 是功能丰富又简单易用的，专门处理 XML 和 HTML 的 Python 官网标准库。

## 2.Xpath 的语法
正则表达式的枯燥无味又学习成本高，Xpath 可以说是不及其万分之一。所以只要花上 10 分钟，掌握 Xpath 不在话下。Xpath 的语言以及如何从 HTML dom 树中提取信息，我将其归纳为“主干 - 树支 - 绿叶”。

### 2-1.“主干” —— 选取节点

抓取信息，我们需知道要从哪里开始抓取。因此，需要找个起始节点。Xpath 选择起始节点有以下可选：

| 表达式     |    描述 | 
| :-------- | :--------|
| nodename  | 选取标签节点的所有子节点。 |
| /  | 从根节点选取，html DOM 树的节点就是 html。 | 
| //  | 从匹配选择的当前节点选择文档中的节点，而不考虑它们的位置。 | 
| .  | 选择当前节点，一般用于二级提取。 | 
| ..  | 选取当前节点的父节点，在二级提取时用到。 | 
| @  | 选取属性。 | 

我们通过以下实例来了解其用法：

| 路径表达式     |    描述   | 
| :-------- | :--------|
| xpath('//div')  | 选取 div 元素的所有子节点。 | 
| xpath('/div')  | 选取 div 元素作为根节点。如果同级有多个 div ，那么所有 div 都会被选为根节点。 | 
| xpath('/div/span')  | 选取属于 div 元素<font color='red'>下</font>所有 span 元素节点。如果 span 有多个，也会被选中。 | 
| xpath('//div') | 选取所有 div 元素节点，不管它们在文档的位置。 | 
| xpath('//div/span')  | 选取 div 元素<font color='red'>下</font>的所有 span 元素节点，不管位于 div 之下的什么位置 | 
| xpath("//@[class='content']")  | 选取包含属性 class 的值为 content 的节点，不管是 div 元素还是其他元素 | 
| xpath("//@[id='center']")  | 选取属性 id 的值为 center 的节点，不管是 div 元素还是其他元素 | 

如果你对于提取节点没有头绪的时候，可以使用通配符来暂时替代。等查看输出内容之后再进一步确认。

| 路径表达式     |    描述   | 
| :-------- | :--------|
| xpath('/div/*') | 选取 div 元素节点<font color='red'>下</font>的所有节点|
| xpath('/div[@*]') | 选取所有带属性的 div 元素节点|

### 2-2.“分支” —— 关系节点与谓语
这一步的过程其实是通过起点一步步来寻找最终包含我们所需内容的节点。我们有时需要使用到相邻节点信息。因此，我们需要了解关系节点或者谓语。

#### 2-2-1.关系节点

一般而言，DOM 树中一个普通节点具有父节点、兄弟节点、子节点。当然也有例外的情况。这些有些节点比较特殊，可能没有父节点，如根节点；也有可能是没有子节点，如深度最大的节点。Xpath 也是有支持获取关系节点的语法。

| 关系 | 路径表达式     |    描述   | 
| :-------- | :-------- | :-------- |
|  parent（父）| xpath('./parent::*') | 选取当前节点的父节点 |
|  ancestor（先辈）| xpath('./ancestor::*') | 选取当前节点的所有先辈节点，包括父、祖父等 |
|  ancestor-or-self（先辈及本身）| xpath('./ancestor-or-self::*') | 选取当前节点的所有先辈节点以及节点本身 |
|  child（子）| xpath('./child::*') | 选取当前节点的所有子节点 |
|  descendant（后代）| xpath('./descendant::*') | 选取当前节点的所有后代节点，包括子节点、孙节点等 |
|  following | xpath('./following ::*') | 选取当前节点结束标签后的所有节点 |
|  following-sibing | xpath('./following-sibing::*') | 选取当前节点之后的兄弟节点 |
|  preceding | xpath('./preceding::*') | 选取当前节点开始标签前的所有节点 |
|  preceding-sibling | xpath('./parent::*') | 选取当前节点之前的兄弟节点 |
|  self（本身）| xpath('./self::*') | 选取当前节点本身 |

#### 2-2-2.谓语

谓语用来查找某个特定的节点或者包含某个指定的值的节点。同时，它是被嵌在方括号中的。

| 路径表达式     |    描述   | 
| :-------- | :--------|
| xpath('./body/div[1]')  | 选取 body 元素节点<font color='red'>下</font>的第一个 div 子节。 | 
| xpath('./body/div[last()]')  | 选取 body 元素节点<font color='red'>下</font>的最后一个 div 子节。 | 
| xpath('./body/div[last()-1]')  | 选取 body 元素节点<font color='red'>下</font>的倒数第二个 div 子节。 | 
| xpath('./body/div[position()-3]')  | 选取 body 元素节点<font color='red'>下</font>的前二个 div 子节。 | 
| xpath('./body/div[@class]')  | 选取 body 元素节点<font color='red'>下</font>的所有带有 class 属性的 div 子节。 | 
| xpath("./body/div[@class='content']")  | 选取 body 元素节点<font color='red'>下</font>的 class 属性值为 centent 的 div 子节。 | 

### 2-3."绿叶" —— 节点内容以及属性
到了这一步，我们已经找到所需内容的节点了。接下来就是获取该节点中的内容了。Xpath 语法提供了提供节点的文本内容以及属性内容的功能。

| 路径表达式     |   描述   | 
| :-------- | :--------|
| text()  | 获取节点的本文内容 |
| @属性  | 获取节点的属性内容 |

具体用法见以下实例：

| 路径表达式     |    描述   | 
| :-------- | :--------|
|  xpath('./a/text()')  | 获取当前节点<font color='purple'>中</font> a 元素节点<font color='red'>中</font>的本文内容 |
| xpath('./a/@href')  | 获取当前节点<font color='purple'>中</font> a 元素节点<font color='red'>中</font>的 href 属性的内容 |

## 3.lxml 的用法
### 3-1.安装 lxml

pip 是安装库文件的最简便的方法，具体命令如下：

```python
pip install lxml
# 如果出现因下载失败导致安装不上的情况，可以先启动 ss 再执行安装命令
# 或者在终端中使用代理
pip --proxy http://代理ip:端口 install lxml
```
### 3-2.使用 lxml
lxml 使用起来是比较简单的。我们首先要使用 lxml 的 etree 将 html 页面进行初始化，然后丢给 Xpath 匹配即可。具体用法如下：

```python
from lxml import etree

html = requests.get(url)           # 使用 requests 请求网页
selector = etree.HTML(html.text)
content = selector.xpath('//a/text()')
```

没错，就这短短几行代码即可完成信息提取。
值得注意的是：xpath 查找匹配返回的类型有可能是一个值，也有可能是一个存放多个值的列表。这个取决于你的路径表达式是如何编写的。