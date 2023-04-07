---
layout: post
title:  "内容提取神器 beautifulSoup 的用法"
date:   2017-07-15 15:57:22 +0800
urlname: 8
cover: https://cdn.jsdelivr.net/gh/monkey-soft/img@master/cover/2017-07-15.jpg
categories: [Python爬虫入门]
tags: [Python, 网络爬虫, beautifulSoup]
keywords: [Python, 网络爬虫, beautifulSoup, 内容提取, bs]
---

上篇文章只是简单讲述正则表达式如何读懂以及 re 常见的函数的用法。

我们可能读懂别人的正则表达式，但是要自己写起正则表达式的话，可能会陷入如何写的困境。

正则表达式写起来费劲又出错率高，那么有没有替代方案呢？

俗话说得好，条条道路通罗马。

目前还两种代替其的办法，一种是使用 Xpath 神器，另一种就是本文要讲的 BeautifulSoup。
<!-- more -->

## 1.BeautifulSoup 简介

引用 BeautifulSoup 官网的说明：

> Beautiful Soup is a Python library for pulling data out of HTML and XML files. It works with your favorite parser to provide idiomatic ways of navigating, searching, and modifying the parse tree. It commonly saves programmers hours or days of work. 

大致意思如下: BeautifulSoup 是一个能从 HTML 或 XML 文件中提取数据的 Python 库。它能通过自己定义的解析器来提供导航、搜索，甚至改变解析树。它的出现，会大大节省开发者的时间。

## 2.安装 BeautifulSoup 

目前 BeautifulSoup 最新版本是 4.6.0，它是支持 Python3的。所以可以大胆去升级安装使用。

安装方法有两种：
- 使用`pip`
比较推荐使用这种方式，既简单又方便管理。

```python
pip install beautifulsoup4
# 如果出现因下载失败导致安装不上的情况，可以先启动 ss 再执行安装命令
# 或者在终端中使用代理
pip --proxy http://代理ip:端口 install beautifulsoup4
```

- 使用`easy_install`

```python
easy_install beautifulsoup4
```

- 使用系统包管理

```python
sudo apt-get install Python-bs4
# 适用于 ubuntu 系统以及 Debian 系统
```

## 3.初始 BeautifulSoup
首先导入 BeautifulSoup 库，然后创建一个 BeautifulSoup 对象，再利用对象做文章。
具体参考示例代码：
```python
from bs4 import BeautifulSoup

soup = BeautifulSoup(response)
print(soup.prettify())
```

上面代码中，response 可以`urlllib`或者`request`请求返回的内容，也可以是本地 HTML 文本。如果要打开本地，代码需要改为
```python
soup = BeautifulSoup(open("index.html"))
# 打开当前目录下 index.html 文件
```

`soup.prettify()`函数的作用是打印整个 html 文件的 dom 树，例如上面执行结果如下：
```html
<html>
 <head>
   <title>
       The Dormouse's story
   </title>  
 </head>  
 <body>
   <p class="title" name="dromouse"><b>The Dormouse's story</b></p>
   <p class="story">Once upon a time there were three little sisters; and their names were</p>
   <a href="http://example.com/elsie" class="sister" id="link1"><!-- Elsie --></a>
 </body>
</html>
```


## 4.解析 BeautifulSoup 对象
想从 html 中获取到自己所想要的内容，我归纳出三种办法：

### 4-1.利用 Tag 对象
从上文得知，BeautifulSoup 将复杂 HTML 文档转换成一个复杂的树形结构,每个节点都是Python对象。

跟安卓中的`Gson`库有异曲同工之妙。节点对象可以分为 4 种：`Tag`, `NavigableString`, `BeautifulSoup`, `Comment`。

Tag 对象可以看成 HTML 中的标签。这样说，你大概明白具体是怎么回事。

我们再通过例子来更加深入了解 Tag 对象。以下代码是以 prettify() 打印的结果为前提。

- 例子1

获取`head`标签内容
```python
print(soup.head)
# 输出结果如下：
<head><title>The Dormouse's story</title></head>
```
- 例子2

获取`title`标签内容
```python
print(soup.title)
# 输出结果如下：
<title>The Dormouse's story</title>
```

- 例子3

获取`p`标签内容
```python
print(soup.p)
# 输出结果如下：
<p class="title" name="dromouse"><b>The Dormouse's story</b></p>
```
如果 Tag 对象要获取的标签有多个的话，它只会返回所以内容中第一个符合要求的标签。


对象一般含有属性，Tag 对象也不例外。它具有两个非常重要的属性， **<font color='red'>name</font>** 和 **<font color='red'>attrs</font>**。

#### 4-1-1.name
name 属性是 Tag 对象的标签名。不过也有特殊的，soup 对象的 name 是 [document]

```python
print(soup.name)
print(soup.head.name)
# 输出结果如下：
[document]
head
```

#### 4-1-2.attrs
attrs 属性是 Tag 对象所包含的属性值，它是一个字典类型。
```python
print(soup.p.attrs）
# 输出结果如下：
{'class': ['title'], 'name': 'dromouse'}
```

其他三个属性也顺带介绍下:
- NavigableString

说白了就是：Tag 对象里面的内容
```python
print(soup.title.string)
 # 输出结果如下：
The Dormouse's story
```

#### 4-1-3.BeautifulSoup

BeautifulSoup 对象表示的是一个文档的全部内容.大部分时候,可以把它当作 Tag 对象。它是一个特殊的 Tag。
```python
print(type(soup.name))
print(soup.name)
print(soup.attrs)
# 输出结果如下：
<type 'unicode'>
[document]
{} 空字典
```

#### 4-1-4.Comment

Comment 对象是一个特殊类型的 NavigableString 对象。

如果 HTML 页面中含有注释及特殊字符串的内容。

而那些内容不是我们想要的，所以我们在使用前最好做下类型判断。例如：
```python
if type(soup.a.string) == bs4.element.Comment:
    ...    # 执行其他操作，例如打印内容
```


### 4-2.利用过滤器
过滤器其实是一个`find_all()`函数， 它会将所有符合条件的内容以列表形式返回。

它的构造方法如下：
```python
find_all(name, attrs, recursive, text, **kwargs )
```

name 参数可以有多种写法：
#### 4-2-1.节点名

```python
print(soup.find_all('p'))
# 输出结果如下：
[<p class="title" name="dromouse"><b>The Dormouse's story</b></p>, <p class="story">Once upon a time there were three little sisters; and their names were</p>]
```

#### 4-2-2.正则表达式

```python
print(soup.find_all(re.compile('^p')))
# 输出结果如下：
[<p class="title" name="dromouse"><b>The Dormouse's story</b></p>, <p class="story">Once upon a time there were three little sisters; and their names were</p>]
```

#### 4-2-3.列表
如果参数为列表，过滤标准为列表中的所有元素。看下具体代码，你就会一目了然了。

```python
print(soup.find_all(['p', 'a']))
# 输出结果如下：
[<p class="title" name="dromouse"><b>The Dormouse's story</b></p>,  <p class="story">Once upon a time there were three little sisters; and their names were</p>,  <a href="http://example.com/elsie" class="sister" id="link1"><!-- Elsie --></a>]
```

另外 attrs 参数可以也作为过滤条件来获取内容，而 limit 参数是限制返回的条数。

### 4-3.利用 CSS 选择器
以 CSS 语法为匹配标准找到 Tag。同样也是使用到一个函数，该函数为`select()`，返回类型也是 list。它的具体用法如下, 同样以 prettify() 打印的结果为前提：

#### 4-3-1.通过 tag 标签查找

```python
print(soup.select(head))
# 输出结果如下：
[<head><title>The Dormouse's story</title></head>]
```

#### 4-3-2.通过 id 查找

```python
print(soup.select('#link1'))
# 输出结果如下：
[<a href="http://example.com/elsie" class="sister" id="link1"><!-- Elsie --></a>]
```

#### 4-3-3.通过 class 查找

```python
print(soup.select('.sister'))
# 输出结果如下：
[<a href="http://example.com/elsie" class="sister" id="link1"><!-- Elsie --></a>]
```

#### 4-3-4.通过属性查找

```python
print(soup.select('p[name=dromouse]'))
# 输出结果如下：
[<p class="title" name="dromouse"><b>The Dormouse's story</b></p>]
```

```python
print(soup.select('p[class=title]'))
# 输出结果如下：
[<p class="title" name="dromouse"><b>The Dormouse's story</b></p>]
```

#### 4-3-5.组合查找

```python
print(soup.select("body p"))
# 输出结果如下：
[<p class="title" name="dromouse"><b>The Dormouse's story</b></p>,
<p class="story">Once upon a time there were three little sisters; and their names were</p>]
```

```python
print(soup.select("p > a"))
# 输出结果如下：
[<a href="http://example.com/elsie" class="sister" id="link1"><!-- Elsie --></a>]
```

```python
print(soup.select("p > .sister"))
# 输出结果如下：
[<a href="http://example.com/elsie" class="sister" id="link1"><!-- Elsie --></a>]
```

## 5.处理上下关系
从上文可知，我们已经能获取到节点对象，但有时候需要获取其父节点或者子节点的内容，我们要怎么做了？这就需要对`parse tree`进行遍历

### 5-1.获取子节点
利用`.children`属性，该属性会返回当前节点所以的子节点。但是它返回的类型不是列表，而是迭代器

### 5-2.获取所有子孙节点
使用`.descendants`属性，它会返回所有子孙节点的迭代器

### 5-3.获取父节点
通过`.parent`属性可以获得所有子孙节点的迭代器

### 5-4.获取所有父节点
`.parents`属性，也是返回所有子孙节点的迭代器

### 5-5.获取兄弟节点
兄弟节点可以理解为和本节点处在统一级的节点，`.next_sibling`属性获取了该节点的下一个兄弟节点，`.previous_sibling`则与之相反，如果节点不存在，则返回 None

注意：实际 HTML 中的 tag 的`.next_sibling`和 `.previous_sibling`属性通常是字符串或空白，因为空白或者换行也可以被视作一个节点，所以得到的结果可能是空白或者换行

### 5-6.获取所有兄弟节点
通过`.next_siblings`和`.previous_siblings`属性可以对当前节点的兄弟节点迭代输出