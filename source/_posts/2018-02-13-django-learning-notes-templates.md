---
layout: post
title:  "Django 学习笔记之模板"
date:   2018-02-13 14:48:08
urlname: 38
cover: https://img.jikehou.cn/cover/2018-02-13.jpg
categories: [Python编程]
tags: [Python, Web开发]
keywords: [Python, web, Django]
---
本文是自己 Django 学习笔记系列的第四篇原创文章。

主要接这篇文章的视图内容，讲解模板的用法。

另外也说下 Django 学习笔记系列的安排。

自己计划大概 15 篇文章的输出自己学习 Django 框架的内容，再用大概 10 篇文章进行实战开发，最后可能用少量的篇幅进行补充。

废话不多说，切回主题。
<!-- more -->

## 1.模板是什么

通过之前文章，我们学会使用 `render(request, 'content.html')` 方法来返回静态页面。

但在一些页面中，页面需要根据不同场景（例如时间，角色）显示不同的数据。这就需要使用到模板（Template）。

模板通常是 HTML 文件，只不过其中带有特定的语句。

这些语句是用来存储并显示数据库中返回的数据。

另外，除了 HTML 文件外，Django的模板也能产生任何基于文本格式的文档。

我们就以一个简单的例子来开始学习模板。该模板是一段添加了些变量和模板标签的 html 文件。

如果你暂时看不懂其中的内容，没有关系，下面会逐步说明。

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>模板（Template）</title>
</head>

<body>
    <p>Dear {{ person_name }},</p>
    <p>It is scheduled to ship on {{ ship_date|date:"F j, Y" }}.</p>

    <ul>
    {% for item in item_list %}
        <li>{{ item }}</li>
    {% endfor %}
    </ul>
    
    {% if ordered_warranty %}
        <p>Your warranty information will be included in the packaging</p>
    {% else %}
        <p>You did not order a warranty.</p>
    {% endif %}
    
    <p>Sincerely,<br />{{ company }}</p>
</body>
</html>
```

根据上面的代码，让我们逐步分析一下其中的内容：
> 用两个大括号括起来的文字（例如 {{ person_name }} ）和 {{ company }} 称为 变量 。这里是插入一个变量的值。赋值是在 视图 （views.py）中操作的。

> 被大括号和百分号包围的文本(例如 {百分号 if ordered_warranty 百分号} )是 模板标签(template tag) 。标签的作用是通知模板系统执行某些操作。

> {百分号 for item in item_list 百分号} 是 模板的 for 标签。有点类似 Python 中的 for 语句，能够让你循环遍历序列中的内容。

> {百分号 if ordered_warranty 百分号} 则是 if 标签，用于执行逻辑判断。

> {{ship_date}} 中用到了 filter 过滤器。这是一种最方便的转换变量输出格式的方式。它的用法跟 Linux 的管道一样，使用管道符 “ | ” 。在这里，我们将变量 ship_date 传递给 date 过滤器，同时指定参数 F  j,Y


## 2.常用标签

从上面的例子中可知，模板中有些常用的标签。让我们来深入了解各个标签的用法。

### 2-1.if/else 标签

{百分号 if 百分号} 标签检查一个变量，如果这个变量为真（即，变量存在，非空，不是布尔值假），系统会显示在 {百分号 if 百分号} 和 {百分号 endif 百分号} 之间的任何内容。

在每一个 {百分号 if 百分号} 标签后面， 一定要用 {百分号 endif 百分号} 标签来关闭。如：

```python
{% if is_rain %}
    <p>外面正在下雨</p>
{% endif %}
```

如果需要 else 操作， 使用 {百分号 else 百分号} 标签。

```python
{% if is_rain %}
    <p>外面正在下雨。</p>
{% else %}
    <p>今天是阴天。</p>
{% endif %}
```

{百分号 if 百分号} 标签用法跟 Python 中的 if 语法有些差别。因此需要重点关注下。

它不支持用圆括号来组合操作，但支持接受 and ， or 或者 not 关键字来对多个变量做判断。如：

```python
{% if sunny and hot %} 
...
{% endif %}

{% if sunny or rainy %} 
...
{% endif %}

{% if not rainy %} 
...
{% endif %}
```
另外{百分号 if 百分号} 并没有 {百分号 elif 百分号} 标签， 请使用嵌套的`` {百分号 if 百分号}`` 标签来达成同样的效果。


### 2-2.for 标签

{百分号 for 百分号} 标签允许我们遍历一个序列上的每一项。

在每一次循环中，模板系统会渲染在 {百分号 for 百分号} 和 {百分号 endfor 百分号} 之间的所有内容。

例如，给定一个 图片列表 image_list，我们可以使用下面的代码来显示这个列表：

```python
<ul>
{% for img in image_list %}
    <li>{{ img.url }}</li>
{% endfor %}
</ul>
```

如果你想将列表反方向输出，可以使用 reversed 关键字。

```python
<ul>
{% for img in image_list reversed %}
    <li>{{ img.url }}</li>
{% endfor %}
</ul>
```

在执行循环之前，通常会先检测列表的大小。模板提供了一个标签 {百分号 empty 百分号}  来输出列表为空的提示。

```python
<ul>
{% for img in image_list %}
    <li>{{ img.url }}</li>
{% empty %} 
    <p>图片列表不能为空.</p>
</ul>
```

除此之外，{百分号 for 百分号} 标签还支持嵌套使用。

```python
{% for page in page_list %}
<ul>
{% for img in image_list %}
     <li>{{ img.url }}</li>
{% endfor %}
</ul>
{% endfor %}
```

跟 {百分号 if 百分号}  标签类似，{百分号 for 百分号} 标签的用法不能等同 Python 中的 for 语句。

它不支持退出循环操作，即 break 语句；同样，它也不支持 continue 语句。

在每个 {百分号 for  百分号}循环中有一个被称为 ** forloop ** 的模板变量。这变量提供一些带有循环进度信息的属性。

* **forloop.counter** 表示当前循环的执行次数的总数。这个计数器是从 1 开始记录，所以在第一次循环操作时，forloop.counter 会被设置为 1。
* **forloop.counter0** 类似于 forloop.counter ，但是它是从0计数的。 第一次执行循环时这个变量会被设置为0。
* **forloop.revcounter** 是记录循环中还没有被遍历项的总数。循环初次执行时 forloop.revcounter 将被设置为序列的长度。 最后一次循环执行中，这个变量将被置1。
* **forloop.revcounter0** 类似于 forloop.revcounter ，但它以0做为结束索引。因此，第一次循环执行的时候，该变量的值为 序列的长度减 1。
* **forloop.first** 是一个布尔值。如果你需要在第一次循环时，执行一些操作。可以利用该属性。
* **forloop.last**也是布尔类型。用法跟 forloop.first 类似。它的运行场景是最后一个循环。


### 2-3.ifequal 标签

比较两个变量的值实在是太常见了，所以 Django 模板提供了 {百分号 ifequal 百分号} 标签提供我们使用。

{百分号 ifequal 百分号} 标签比较两个值，当它们相等时，显示在 {百分号 ifequal 百分号} 和 {百分号 endifequal 百分号} 之中所有的值。

如下面的例子是比较两个模板变量 name 和 currentname：

```python
{% ifequal name curentname %}
<p>名字是一样。</p>
{% else %}
<p>名字是不一样。</p>
{% endifequal %}
```
除了判断两个变量的值，该标签还支持字符串，整数和小数做为参数，但是不支持 Python 的列表类型、布尔类型和字典类型。

模板还有一个比较不相等的 **ifnotequal** 标签，它的用法跟 ifequal 标签类似。


### 2-4.注释标签
如果是需要对单行进行注释操作，使用 # # 标签：

```python
{# 单行注释 #}
```

如果要实现多行注释，需用到 {百分号 comment 百分号} 模板标签，就像这样：

```python
{% comment %}
多行注释
{% endcomment %}
```


## 3.上下文(context)对象

context 对象视图和模板文件的承接桥梁。 context 对象携带视图中需要填充的数据，然后在模版渲染的时候，将数据赋值给模板的变量。

模板进而可以渲染显示。

让我们通过下面的例子来了解 context 的用法。在 views.py 中，我们创建一个 current_time 视图，然后用 Django 模板系统修改视图。

其内容如下：
```python
from django.template import Template, Context
from django.http import HttpResponse

def current_time(request):
    now = '2018-2-27 20:01:56'
    t = Template("<html><body>It is now {{ time }}.</body></html>")
    html = t.render(Context({'time': now}))
    return HttpResponse(html)
```

context 和 Python 字典很类似，它以键值对的形式传递参数。

context 不仅能传递字符穿和 datetime.date 这样的简单参数值，还能处理更加复杂的数据结构，例如列表、字典和类的对象。

模板遍历复制数据结构是用到句点符号(.)。

下面是向模板传递一个 Python 字典的例子。

```python 
from django.template import Template, Context
from django.http import HttpResponse

def case_dir(request):
    person = {'name': '极客猴', 'age': '18'}
    t = Template('{{ person.name }} is {{ person.age }} years old.')
    html = t.render(Context({'person': person}))
    return HttpResponse(html)
```

向模板传递一个类的对象的列子：

```python
# 在其它目录有一个实体类
class Person(object):
def __init__(self, name, age):
self.name, self.age = name, age

# 在 views.py 文件中
from django.template import Template, Context
from django.http import HttpResponse

def case_class(request):
    t = Template('{{ person.name }} is {{ person.age }} years old.')
    html = t.render(Context({'person': Person('极客猴','18')}))
    return HttpResponse(html)
```

默认情况下，如果一个变量不存在，模板系统会把它展示为空字符串，不会引发一个异常。


## 4.加载模板

Django 提供模板功能目的是为了让视图和前端页面内容隔开来。

同时，前端设计师可能对 HTML 编码比较熟悉，但完全不懂 Python。

Python 工程师不一定都熟悉前端的知识。

因此，不提倡直接在 视图中混入模板内容。

views.py 中的视图函数只负责加载模板文件，模板一般存放到 templates 文件夹中。

Django 提供了一种使用方便且功能强大的 API，用于从本地中加载模板。

当你新建一个新的 Django 项目时，在 setting.py 配置文件中有个 **TEMPLATES** 选项。

TEMPLATES 的 DIRS 属性是记录存放模板文件的绝对路径。

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

我们无需手动去配置 DIRS，当我们创建目录时，Django 会自动帮我们填充好路径。

如果你在 application 目录中创建名为 templates 目录，你会发现 setting.py 中的 TEMPLATES 选项发生变化。

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, 'templates')]
        ,
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

了解 Django 的机制之后，我们就可以加载模板文件了。

因为 Django 已经帮我们搞定模板文件搜索工作。加载模板，我们使用函数  django.template.loader.get_template()，而不是手动从文件系统中加载。

调用 get_template() 函数，需要传入模板文件名称的参数。

get_template() 函数帮我们实现了打开模板文件，关闭模板文件，异常处理等工作。

这大大减轻了我们重复的工作量。

```python
from django.template.loader import get_template
from django.template import Context
from django.http import HttpResponse

def current_time(request):
    now = '2018-2-27 20:01:56'
    t = get_template('current_time.html')
    html = t.render(Context({'time': now}))
    return HttpResponse(html)
```