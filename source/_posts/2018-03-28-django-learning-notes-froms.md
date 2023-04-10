---
layout: post
title:  "Django 学习笔记之表单"
date:   2018-03-28 11:29:46
urlname: 42
cover: https://img.jikehou.cn/cover/2018-03-28.jpg
categories: [从零学Django]
tags: [Python, Web, Django]
keywords: [Python, web, Django]
---
本文是自己 Django 学习笔记系列中第 8 篇，算是基础知识篇章中最后一篇笔记。

后续的笔记内容会相对比较综合。所以建议大家要把前面的内容，包括本篇笔记掌握。而本篇内容主要是讲解表单。
<!-- more -->
## 1.表单是什么？
表单英文单词是 Froms, 它其实属于 HTML 的知识范畴。

HTML 表单可以实现用户和 Web 站点之间数据交互。表单允许用户将数据发送到 Web 站点。

但在大多数情况下，Froms 携带的数据发送到 Web 服务器，Web  页面会将其拦截并自己使用它。

举个栗子，用户使用浏览器访问一个页面，在页面的搜索框中输入图书的名称，想获取所有销售该图书的商店。

Web 站点需要获取图书名称的信息作为数据库查询条件，所以将数据拦截并获取图书的名称。

然后通关查询数据库，最后将查询到的所有商店信息返回给浏览器进行渲染显示。

另外，博客系统中的评论模块也是这个原理。因此，在一些站点上会爆出 XSS 漏洞。

原因可能是编码者没有对用户提交的数据进行过滤或者过滤不严，直接存储到数据库中。


## 2.HTML 表单
这部分是给不熟悉 HTML 表单同学准备的，如果你已经掌握这部分知识。可以选择直接跳过。

HTML 表单在页面中表现是一个可以填写数据的区域。

表单中会根据页面显示需求，采用不同的表单元素来呈现，比如：文本域(textarea)、下拉列表、单选框(radio-buttons)、复选框(checkboxes)等等


它可能长得这个样子

![它可能长得这个样子](https://img.jikehou.cn/img/70_1.png)

表单使用标签 **form** 来设置区域范围，它携带常用的属性如下：
```html
<form action="search.html" method="GET" target="_blank" >
    <!-- 
    ...
    表单元素
    -->
</form> 
```

**action 属性**：指定表单数据提交到哪个页面。例子中是提交到 search.html 页面，这个也会跳转到 search.html 页面。如果你想把数据提交到原来的页面，action 的值为空就行，即 **action=""**

**method 属性**：规定提交表单时所用的 HTTP 方法，一般选择**GET 或者 POST**。

**target 属性**：规定 action 属性中地址的目标（默认：_self）。如果填写值 **_blank**，当点击按钮提交数据时，在新窗口中打开新的页面。


常用表单元素有以下这些：
```html
<!DOCTYPE html>
<html>
<body>
    <form action="" method="GET" >
        <!-- label 标签用于显示内容，不可以输入 -->
        <label >我是 label 标签</label><br>
        <!-- input 元素 -->
        <!-- 单行的文本输入框 -->
        <input type="text" name="lastname"><br>
        <!-- 单选按钮 -->
        <input type="radio" name="sex" value="female">female<br>
        <!-- 提交按钮, 用于提交表单数据 -->
        <input type="submit" value="submit">
        <!-- 还有其他的表单元素, 就不一一列举 -->
    </form> 
</body>
</html>
```

对于每个输入字段 ，必须设置一个 `name 属性`，数据才会被正确提交。

因为页面是通过 name 属性中的值来获取用户输入的内容的。

以 GET 方式请求为例，有个单行输入框定义 **name="q"**。当你在输入框中填写值 moneky 然后提交。

你会发现浏览器地址发生变化了，从之前的 `127.0.0.1:8000` 变为 `127.0.0.1:8000/?q=monkey`


## 3.Django Form 

### 3-1.功能
Django 的表单针对 HTML 表单实现了一层封装，这使得 Django 的 Form 表单功能更加强大。它具有以下功能：
1）自动生成HTML表单元素
2）检查表单数据的合法性
3）如果验证错误，重新显示表单（数据不会重置）
4）数据类型转换（字符类型的数据转换成相应的Python类型）


### 3-2.Form 对象

Objects Form对象封装了一系列 Field 和验证规则，Form 类都必须直接或间接继承自 `django.forms.Form`，定义 Form 有两种方式：


**方法一：根据 Model 自动生成 Form**

如果你的需求比较简单，只想将模型的字段全部以表单的形式展示出来，你可以采用这种方法。
```python
from django import forms

class AuthorFormOne(forms.Form):
    name = forms.CharField(max_length=40,label='名称')
    email = form.EmailField()
    message = form.CharField(widget=forms.TextInput)
```

Form 表单除了定义属性跟模型差不多，它还具有一些特有的属性。
1) **Widget**
用来渲染成 HTML 元素的工具，如：forms.TextInput 对应 HTML中的 **input标签**
2) **Form**
一系列 Field 对象的集合，负责验证和显示 HTML 元素。
3) **Form Media**
用来渲染表单的 CSS 和 JavaScript 资源。

**方法二：自定义 Form**

自定义表单是比较高级用法，有时候通过 Model 自动创建的 Form 无法满足自己需求。

譬如：Model 中的某些属性我不需要显示在页面上，或数据处理方式比较复杂，这个时候你就需要自定义 Form。自定义 Form 是直接继承 Form

```python
# 在 models.py 定义模型 Author
class Author(models.Model):
    name = models.CharField(max_length=40)
    email = models.EmailField()

# 新建 forms.py 文件, 用于定义 Form
class AuthorFormTwo(ModelForm):
    class Meta:
        model = Author
        fields = ('name',)  # 只显示 model 中指定的字段
```


### 3-3.视图层的处理
在视图文件 view.py 中， 可以获取、过滤到用户提交的数据。
```python
from django.http import HttpResponseRedirect
from django.shortcuts import render

# Create your views here.
from demo_form.form.forms import AuthorFormOne

def formView(request):
    # 过滤 POST 方法的请求
    if request.method == "POST":
        form = AuthorFormOne(request.POST)
        # 验证表单
        if form.is_valid():
            # 一般过滤数据
            name = form.cleaned_data['name']
            email = form.cleaned_data['email']
            information = form.cleaned_data['information']
            # 处理业务, 如查询数据库信息
            return HttpResponseRedirect('/')
    else:
        # 不是 GET 请求则显示表单
        form = AuthorFormOne()

    templateView = 'author.html'
    return render(request, templateView, {'form':form})
```

form.is_valid() 返回 true 后，表单数据都被存储在 form.cleaned_data 对象中（字典类型，意为经过清洗的数据）。

而且数据会被自动转换为 Python 对象，如：在 form 中定义了 DateTimeField ，那么该字段将被转换为 datetime 类型。

而模板文件内容则比较简单，使用几个 HTML 标签以及模板标签就轻松搞定。
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>author 表单</title>
</head>

<body>
    <form action='/forms/' method='POST'>
        {% csrf_token %}
        {{ form }}
        <input type="submit" value="提交" />
    </form>
</body>
</html>
```

我在 url.py 定义表单的路径是：
```python
from django.contrib import admin
from django.urls import path

from demo_form import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('forms/', views.formView),
]
```

所以使用浏览器访问 `http://127.0.0.1:8000/forms/`， 你会发现页面会自动渲染出表单的信息。

![你会发现页面会自动渲染出表单的信息](https://img.jikehou.cn/img/70_2.png)


### 3-4.美化模板
我们虽然成功把表单内容渲染到页面上，但是页面有点丑陋。你可能会无法忍受，想把页面修改得美观一点，顺便也秀秀自己的 Bootstrap 知识。

Django 默认提供几种显示表单的方式。例如 **form.as_p**、**form.as_table**、**form.as_ul**，在 html 文件中会被渲染成 **p 标签**，**table 标签**和 **ul 标签**。

除此之外，还可以 form 还支持自定义。具体实现是你获取到 form 中每个属性，然后逐一渲染指定样式。

所以 author.html 经过调整之后的代码如下：
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>author 表单</title>
</head>
<body>
    <form action='/forms/' method='POST'>
        <div class="">
            {{ form.name.errors }}
            <label for="name">姓名：</label><br>
            {{ form.name }}
            <br>
        </div>

        <div class="">
            {{ form.email.errors }}
            <label for="email">邮箱：</label><br>
            {{ form.email }}
            <br>
        </div>

        <div class="">
            {{ form.information.errors }}
            <label for="information">个人信息：</label><br>
            {{ form.information }}
            <br>
        </div>

        <p><input type="submit" value="提交" /><p>
    </form>
</body>
</html>
```

## 4.源码

如果你想获取项目的源码, 点击按钮进行下载。

{% btn 'https://github.com/monkey-soft/Django_demo', 源码下载, far fa-hand-point-right,blue larger %}