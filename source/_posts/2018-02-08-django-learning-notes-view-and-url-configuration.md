---
layout: post
title:  "Django 学习笔记之视图与URL配置"
date:   2018-02-08 18:34:54
urlname: 37
cover: https://img.jikehou.cn/cover/2018-02-08.jpg
categories: [从零学Django]
tags: [Python, Web, Django]
keywords: [Python, web, Django]
---
本文章是自己学习 Django 框架的第三篇。前面两篇文章主要记录 Django 理论相关知识。

从本篇文章开始，将以理论和实战方式讲述 Django 框架的知识。

让我们一起来 coding 吧~
<!-- more -->
## 1.新建项目
我们开发 Web 程序是基于 Django 框架，所以要想创建 Django 项目。

创建项目有两种方式，一种是使用 Django 管理任务 **django-admin.py**，另一种是借助 IDE 工具 **Pycharm**。

### 使用 django-admin.py

**1）新建project**

在你准备存放项目的目录下，打开终端，执行新建命令。
```python
django-admin.py startproject Django_demo  // Django_demo 为 project 的名称，你可随意命名
// 如果执行失败，可以改用下面命令
django-admin startproject Django_demo     // Django_demo 为 project 的名称，你可随意命名
```

新建 Project 成功之后，你会发现目录下会多出一些文件。这些文件主要跟工程配置有关系，跟具体业务逻辑没啥关系。
![新建 Project 成功](https://img.jikehou.cn/img/20180208_1.png)

**2）新建 application**

新建了项目，为什么还要新建 application ？

简单来说 **project **可以理解为一个容器，**application** 可以理解为 project 中的一个网站应用。

对于每个Django项目有且只有一个 project, 而一个 project 可以包含多个 application。

到最外层的 Django_demo 目录下新建我们的 application
```python
python manage.py startapp test    // test 为 application 的名称，你可随意命名
```
新建成功后，会发现多了一个名为 test 目录。看到里面有些文件，是否心中有种似曾相识的感觉？

![test 目录](https://img.jikehou.cn/img/20180208_2.png)

**3）配置应用**

最后一步，我们需要把刚才新建的 application 加到 settings.py 中的 INSTALL_APPS 中 。

具体操作是修改 Django_demo/Django_demo/settings.py 文件，

```python
INSTALLED_APPS = (
    'django.contrib.admin',           // 管理站点
    'django.contrib.auth',            // 认证系统
    'django.contrib.contenttypes',    // 用于内容类型框架
    'django.contrib.sessions',        // 回话框架
    'django.contrib.messages',        // 消息框架
    'django.contrib.staticfiles',     // 管理静态文件的框架
     // Djaogo 默认包含上面的应用
    'test',   // 刚才新建的 application，如果有多个 application，依次在后面添加即可。
)
```

### 借助 Pycharm

有些小伙伴可能使用命令工具方式比较复杂，是否有比较简便的方式？当然，那借助 Pycharm，直接一步搞定。

打开 PyCharm IDE 工具，点击 File -> New Project， 左边选择Django。新建如下图所示： 

![建如下图所示](https://img.jikehou.cn/img/20180208_3.png)

新建成功之后会看到这样的目录结构 ，Pycharm 已经帮我们搞定了大部分工作。

![Pycharm 已经帮我们搞定了大部分工作](https://img.jikehou.cn/img/20180208_4.png)


## 2.视图与URL配置
### 2-1.第一个页面
**URL配置(URLconf.py) 是文件路由，是 URL 和 URL 对应视图的映射表** 。

换句话说，就是由它来分发网络请求，将每个 Web 请求根据 URL 地址来调用视图来显示。

URL 模式的语法是：
```python
urlpatterns = [
    '''
    url(路径匹配, view 函数, 可选参数, 可选别名),
    路径匹配：  一个正则表达式字符串。一般写法是  **r'正则表达式'**。如果你对正则表达式不太熟悉，建议你先补习补习下。
    view 函数： 一个可调用对象，通常为一个视图函数或一个指定视图函数路径的字符串
    可选参数：  可选的要传递给视图函数的默认参数（字典形式）
    可选别名：   可选参数，一般结合模板方便管理
    '''
    url(r'^admin/', admin.site.urls),
]
```

**一般在 URLconf.py 中新增一个 URL 表达式，就需要在 view.py 中增加一个视图函数**。

我们来根据上述规则创建下首个页面。首先在 view.py 中增加首页视图函数。
```python
# Create your views here.
from django.http import HttpResponse

def index(request):
    return HttpResponse("Hello ！这是我第一个 Django 项目")
```

视图中的函数名 index 对应是 URL 地址中的 path 部分。 

那什么是 path 呢？URL 地址定义是 **协议://host:port/path** 。

假如有个地址是 **http://127.0.0.1:8000/music**。 

http 就是协议；127.0.0.1 是主机号，主机号可以是 IP 地址或者域名，不过域名是比较常见，因为容易记；

port 是端口号，如果端口号是 80 可以忽略不写，其他则要填写完整；

music 就是前面说的 path，端口号后面第一个 "/" 符号后面的字符串都是路径（path）。

index 比较特殊，我们通常把 index 设定为首页，所以访问首页的时候，path 不用填写。

如果你还不很了解，我们等会运行下程序，你就会恍然大悟。

在创建视图函数之后，我们需要在 urls.py 中配置好 url 匹配规则。
```python
from django.conf.urls import url
from django.contrib import admin
from demo import views                # 导入我们的视图

urlpatterns = [
    url(r'^admin/', admin.site.urls),
    url(r'^$', views.index),          # 添加首页 url 匹配规则。index 就是刚才创建的函数。
]
```

配置了 url 匹配规则，剩下最后一步运行验证了。点击 pycharm 工具上的运行按钮（绿色的播放键），然后打开浏览器，在地址栏输入 **http://127.0.0.1:8000**。 你会看到我们 index 函数返回的内容。

如果你访问的地址是 **http://127.0.0.1:8000/**，同样也是能正常看到页面内容。

![浏览器访问地址看到的内容](https://img.jikehou.cn/img/20180208_5.png)

pycharm 能启动一个 web 服务器，内部是使用到 manage.py 脚本。因此，我们也可以使用命令行的形式来启动一个 web 本地测试服务器。在最外层的 Django_demo 目录下，打开终端命令行工具，执行 `python manage.py runserver` 即可启动服务器。

### 2-2.返回 HTML 页面
视图返回结果是一串字符串，我们只是用于做测试用的。

但是实际开发中，返回结果通常是 html 页面。

view 函数想要返回 html 页面，使用 render() 携带一个 html 页面即可。

render() 内部返回的也是一个 HttpResponse 对象。

我们在 view.py 中增加名为 **content** 的视图函数，用来返回一个 html 页面。
```python
# Create your views here.
from django.http import HttpResponse

def content(request):
    return render(request, 'content.html')
```

接着在 application 目录下新建名为 templates 的文件夹，再创建一个 content.html 文件。
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>content</title>
</head>

<body>
    <h1>这是 content 页面。</h1>
    <h2>这是 content 页面。</h2>
    <h3>这是 content 页面。</h3>
</body>
</html>
```

还要配置下 url 路由规则。
```python
from django.conf.urls import url
from django.contrib import admin
from demo import views                # 导入我们的视图

urlpatterns = [
    url(r'^admin/', admin.site.urls),
    url(r'^content/$', views.content)   # 添加 content 页面的 url 匹配规则。
]
```

最后一步，使用浏览器浏览 content 的页面。

![使用浏览器浏览 content 的页面。](https://img.jikehou.cn/img/20180208_6.png)

### 2-3.url 路由的命名组
url 路由有两种命名组，一种是无名名组，另一种是有名分组。 无名分组是使用简单的、没有命名的正则表达式组（通过圆括号）来捕获 URL 中的值。上述两个例子和以下的一些都是都是无命名分组。
```python
from django.conf.urls import url
from demo import views

urlpatterns = [
    url(r'^content/2018/$', views.special_case_2018),
    url(r'^content/([0-9]{4})/$', views.year_content),
    url(r'^content/([0-9]{4})/([0-9]{2})/$', views.month_content),
    url(r'^content/([0-9]{4})/([0-9]{2})/([0-9]+)/$', views.detail_content)
]
```
如果返回的 URL 的 path 为 /content/2018 时，是无法匹配到上面任何一个模式，因为每个模式要求 URL 以一个斜线结尾。
path 为 /content/2018/ 则能匹配到两个模式，是匹配到哪个呢？

因为模式按照顺序来匹配的，所以第一个模式会被优先匹配。

path 为 /content/2018/02/ 将匹配到第三个模式。

Django 调用的是 views 文件中的 month_content(request, '2018', '02')。

无名分组的视图只能接受 python 中传入的固定值参数，如值 2018 等。

但是无法获取到存放值的变量，而有名分组恰恰能解决这个痛点。

有名分组只是在无名分组的正则表达式上增加一个参数即可。

语法是(?P<name>pattern)，其中 name 是组的名称，pattern 是要匹配的模式。具体改造如下：

```python
from django.conf.urls import url
from demo  import views

urlpatterns = [
    url(r'^content/2018/$', views.special_case_2018),
    url(r'^articles/(?P<year>[0-9]{4})/$',  views.year_content),
    url(r'^articles/(?P<year>[0-9]{4})/(?P<month>[0-9]{2})/$', views.month_content),
    url(r'^articles/(?P<year>[0-9]{4})/(?P<month>[0-9]{2})/(?P<day>[0-9]{2})/$', views.detail_content)
]
```

经过改造之后，当访问 path 为 /content/2018/02/ 时，请求将调用views.content(request, year='2018', month='02')函数，而不是views.month_content(request, '2018', '02')。


### 2-4.路由转发器（include）
如果一个项目下有很多的 application，那么在 urls.py 里面就要写巨多的 urls 映射关系。

这样看起来很不灵活，而且杂乱无章。

这时候就要根据不同的 application 来分发不同的url请求。

假如在上述的 project 中，我又新建了一个新的 application，名为 app02。

配置路由转发器操作如下：首先，在 urls.py 里写入 urls 映射条目。

值得注意是，要导入Django 的 include 方法。
```python
from django.conf.urls import url, include
from django.contrib import admin

urlpatterns = [
    url(r'^admin/', admin.site.urls),
    url(r'^app02/', include('app02.urls')),   // 将 url 为”app02/“的请求都交给 app02 下的 urls 去处理
]
```

其次，在 app02 目录下创建一个 urls.py 文件，用来处理请求的url，使之与 views 建立映射。
```python
from django.conf.urls import include, url
from app02 import views
 
urlpatterns = [
    url(r'^admin/', admin.site.urls),
    url(r'index/$', views.index),
]
```

