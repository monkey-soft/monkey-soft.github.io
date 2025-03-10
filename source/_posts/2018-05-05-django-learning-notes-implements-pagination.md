---
layout: post
title:  "Django 实现分页功能"
date:   2018-05-05 14:10:03
urlname: 51
cover: https://img.jikehou.cn/cover/2018-05-05.jpg
categories: [Python编程]
tags: [Python, Web开发]
keywords: [Python, web, Django]
---
当页面因需要展示的数据条目过多，导致无法在一个页面全部显示。

这时，页面经常会采用分页形式进行展示，然后每页显示 20 或者 50 等条数据。分页经常在网站上随处可见，

它大概是这样子：
<!-- more -->
![它大概是这样子](https://img.jikehou.cn/img/20180505_Bootstrap-Paginator.jpg)

这样的实现不仅提高了用户体验，还是减轻数据库读取数据的压力。

Django 自带名为 Paginator 的分页工具， 方便我们实现分页功能。本文就讲解如何使用 Paginator 实现分页功能。

## 1.Paginator
Paginator 类的作用是将我们需要分页的数据分割成若干份。

当我们实现化一个 Paginator 类的实例时，需要给 Paginator 传入两个参数。

第一个参数是数据源，可以是**一个列表、元组、以及查询结果集 QuerySet**。

第二个参数**需要传入一个整数，表示每页显示数据条数**。具体写法如下：

```python
book_list = []
for x in range(1, 26):  # 一共 25 本书
    book_list.append('Book ' + str(x))
# 将数据按照规定每页显示 10 条, 进行分割
paginator = Paginator(book_list, 10)
```

上面代码中，我们传入一个名为 book_list 的列表，该列表中含有 25 本书，然后我们给 Paginator 设定每页显示 10 条数据，最后得到一个 Paginator 实例。 

另外 Paginator 类中有三个常用的属性，它们分别是：
- **count**：表示所有页面的对象总数。
- **num_pages**： 表示页面总数。
- **page_range**： 下标从 1 开始的页数范围迭代器。

## 2.Page 对象
Paginator 类提供一个 `page(number)` 函数，该函数返回就是一个 Page 对象。

参数 number 表示第几个分页。

如果 number = 1，那么 page() 返回的对象是第一分页的 Page 对象。

在前端页面中显示数据，我们主要的操作都是基于 Page 对象。具体用法如下：

```python
# 使用  paginator 对象返回第 1 页的 page 对象
books = paginator.page(1)
```

Page 对象有三个常用的属性：
- `object_list`: 表示当前页面上所有对象的列表。
- `numberv`: 表示当前页的序号，从 1 开始计数。
- `paginator`： 当前 Page 对象所属的 Paginator 对象。

除此之外，Page 对象还拥有几个常用的函数：
- `has_next()`： 判断是否还有下一页，有的话返回True。
- `has_previous()`：判断是否还有上一页，有的话返回 True。
- `has_other_pages()`：判断是否上一页或下一页，有的话返回True。
- `next_page_number()`： 返回下一页的页码。如果下一页不存在，抛出InvalidPage 异常。
- `previous_page_number()`：返回上一页的页码。如果上一页不存在，抛出InvalidPage 异常。

## 3.运用
下面是自己编写的 demo 程序，介绍 Paginator 和 Page 如何一起使用。

### 3-1.视图
在 views.py 获取需要展示的全部数据，然后使用 Paginator 类对数据进行分页，最后返回第 1 页面的 page 对象。p

age 对象的作用巨大，一方面展示当前分页数据，还提供获取后续页面数据的接口。
```python
from django.core.paginator import Paginator, PageNotAnInteger, EmptyPage, InvalidPage
from django.http import HttpResponse
from django.shortcuts import render

def paginator_view(request):
    book_list = []
    '''
    数据通常是从 models 中获取。这里为了方便，直接使用生成器来获取数据。
    '''
    for x in range(1, 26):  # 一共 25 本书
        book_list.append('Book ' + str(x))

    # 将数据按照规定每页显示 10 条, 进行分割
    paginator = Paginator(book_list, 10)

    if request.method == "GET":
        # 获取 url 后面的 page 参数的值, 首页不显示 page 参数, 默认值是 1
        page = request.GET.get('page')
        try:
            books = paginator.page(page)
        # todo: 注意捕获异常
        except PageNotAnInteger:
            # 如果请求的页数不是整数, 返回第一页。
            books = paginator.page(1)
        except InvalidPage:
            # 如果请求的页数不存在, 重定向页面
            return HttpResponse('找不到页面的内容')
        except EmptyPage:
            # 如果请求的页数不在合法的页数范围内，返回结果的最后一页。
            books = paginator.page(paginator.num_pages)

    template_view = 'page.html'
    return render(request, template_view, {'books': books})
```

### 3-2.模板
模板的工作就是在 HTML 页面中填充数据。

当拿到视图传递过来的 books（books 是一个 Page 对象），就在 for 循环中打印数据。

最后使用 books 根据页面情况展示上一页按钮，当前页数，总页数，下一页按钮。

```python
{% load staticfiles %}
<!DOCTYPE html>
<html lang="en" xmlns="http://www.w3.org/1999/html">
<head>
    <meta charset="UTF-8">
    <link href="{% static 'css/bootstrap.min.css' %}" rel="stylesheet">
    <script src="{% static 'js/jquery.min.js' %}"></script>
    <script src="{% static 'js/bootstrap.min.js' %}"></script>
    <title>分页</title>
</head>

<br>
    <div class="text-center" >
        {% for book in books %}
            <span>书名： {{ book }} <br /></span>
        {% endfor %}
    </div>

    {# 实现分页标签的代码 #}
    {# 这里使用 bootstrap 渲染页面 #}
    <div id="pages" class="text-center" >
        <nav>
            <ul class="pagination">
                <li class="step-links">
                {% if books.has_previous %}
                    <a class='active' href="?page={{ books.previous_page_number }}">上一页</a>
                {% endif %}

                <span class="current">
                    Page {{ books.number }} of {{ books.paginator.num_pages }}</span>

                {% if books.has_next %}
                    <a class='active' href="?page={{ books.next_page_number }}">下一页</a>
                {% endif %}
                </li></ul></nav></div>
</body>
</html>
```

### 3-3.运行结果

我在 urls.py 中配置当前的 app 访问路径是 `paginator/`。

所以使用浏览器访问地址 `http://127.0.0.1:8000/paginator/`， 看到访问结果如下：

![看到访问结果-1](https://img.jikehou.cn/img/20180505_1.png)

![看到访问结果-2](https://img.jikehou.cn/img/20180505_2.png)

![看到访问结果-3](https://img.jikehou.cn/img/20180505_3.png)

## 4.源码

如果你想获取项目的源码, 点击按钮进行下载。

{% btn 'https://github.com/monkey-soft/Django_demo', 源码下载, far fa-hand-point-right,blue larger %}

