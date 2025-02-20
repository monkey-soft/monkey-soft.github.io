---
layout: post
title:  "Django 实战2：利用 Session 实现自动登录"
date:   2018-05-12 20:51:31
urlname: 53
cover: https://img.jikehou.cn/cover/2018-05-12.jpg
categories: [从零学Django]
tags: [Python, Web, Django]
keywords: [Python, web, Django, session, 项目实战]
---
上篇文章中讲到 Django 如何启动以及配置 sessions 功能。sessions 功能用是跟踪用户的状态，经常结合 Cookie 功能实现自动登录功能。 

所谓的“自动登录”指的是：我们登录一些网站，在不关闭浏览器以及距离上次登录时间不是很长的情况下。

无论我们在新的标签页打开网站，还是关闭页面重新打开网站，登录状态一直保持着。

本文内容有两个：一是利用 Django 实现自动登录功能，二是揭开“自动登录”的神秘面纱。
<!-- more -->

## 1.新建项目
我为了将本系列所有文章的示例代码保持集中状态，所以直接在 **Django_demo** 项目中创建应用。

如果第一次看这文章，需要先创建项目(project)，再创建应用(app)。

我新建的应用是 `demo_session`。

![我新建的应用是 demo_session](https://img.jikehou.cn/img/20180512_1.png)

然后在 setting.py 中启动请用，并检查 sessions 组件是否启动。

![检查 sessions 组件是否启动](https://img.jikehou.cn/img/20180512_2.png)

因为需要 Cookie 功能，所以同样需要在 settings.py 增加一些配置。
```python
SESSION_COOKIE_NAME = "sessionid"       # Session的cookie保存在浏览器上时的key
SESSION_COOKIE_PATH = "/"               # Session的cookie保存的路径(默认)
SESSION_COOKIE_DOMAIN = None            # Session的cookie保存的域名(默认)
SESSION_COOKIE_SECURE = False           # 是否Https传输cookie
SESSION_COOKIE_HTTPONLY = True          # 是否Session的cookie只支持http传输(默认)
SESSION_COOKIE_AGE = 1209600            # Session的cookie失效日期(2周)(默认)
SESSION_SAVE_EVERY_REQUEST = False      # 是否设置关闭浏览器使得Session过期
SESSION_COOKIE_AT_BROWSER_CLOSE = False  # 是否每次请求都保存Session，默认修改之后才能保存
```

如果你将 **SESSION_SAVE_EVERY_REQUEST** 设置为 True， 那么关闭浏览器之后，需要重新登录。

## 2.流程

应用中会涉及到 3 个页面，所以我绘制流程图帮助理解。

![绘制流程图帮助理解](https://img.jikehou.cn/img/20180512_3.png)

## 3.实现
### 3-1.新建 model
服务器接收到浏览器传送过来登录信息，需要验证账号和密码等信息。

所以需要新建 model 保存信息，以便后续跟数据库做校验。

这里我只是简单保存信息，登录验证后续讲解。
```python
class User(models.Model):
    username = models.CharField(max_length=20)  # 账号
    password = models.CharField(max_length=20)  # 密码
    nickname = models.CharField(max_length=20)  # 昵称
```

### 3-2.新建 form
用户将登录信息发送给服务器是用到 POST 请求，所以需要创建表单。

在应用目录下新建名为 forms 目录，然后创建 forms.py 文件。

```python
from django.forms import ModelForm, TextInput, PasswordInput
from demo_session.models import User

class UserForm(ModelForm):
    class Meta:
        model = User
        fields = ['username', 'password', ]  # 只显示 model 中指定的字段
        # 指定呈现样式字段、指定 CSS 样式
        widgets = {
            'username': TextInput(attrs={'class': 'text',
                                         'value': 'monkey'}),
            'password': PasswordInput(attrs={'value': '13245678', })
        }
```

### 3-3.新建视图
页面一共有三个，分别是登录、首页、登出。具体实现如下：

```python
# view.py
from django.http import HttpResponseRedirect, HttpResponse
from django.shortcuts import render
from demo_session.form.forms import UserForm

# 用户登录
def login_view(request):
    # 过滤 POST 方法的请求
    if request.method == 'POST':
        userfrom = UserForm(request.POST)
        # 验证表单
        if userfrom.is_valid():
            username = userfrom.cleaned_data['username']
            password = userfrom.cleaned_data['password']
            # ... 执行验证登录信息操作

            # 将等你信息传递给 Session 对象, 实际应用中不建议这么操作
            request.session['username'] = username
            # 跳转到页面
            return HttpResponseRedirect('/index/')
    else:
        # 不是 GET 请求则显示表单
        userfrom = UserForm()
    template_view = 'login.html'
    return render(request, template_view, {'userfrom': userfrom})

# 成功登录之后, 跳转首页
def index_view(request):
    username = request.session.get('username', '')
    # print(username)
    template_view = 'index.html'
    return render(request, template_view, {'username': username})

# 登出操作
def logout_view(request):
    # 删除 session
    del request.session['username']
    return HttpResponse('登出成功')
```

### 3-4.对应模板

视图 login_view, index_view 对应的模板是 login.html, index.html。

其中 login.html 的实现如下：

```html
{% load staticfiles %}
<!DOCTYPE html>
<html lang="{{ LANGUAGE_CODE|default:"en-us" }}">
<head>
    <meta charset="UTF-8">
    <title>登录</title>
    <link href="{% static 'css/style.css' %}" rel="stylesheet" type='text/css' >
    <!--webfonts-->
    <link href="{% static 'css/font.css' %}" rel='stylesheet' type='text/css'>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <script type="application/x-javascript">
            addEventListener("load", function() { setTimeout(hideURLbar, 0); }, false);
            function hideURLbar(){ window.scrollTo(0,1); }</script>
</head>
<body>
    <div class="main">
        <div class="login-form">
        <h1>Member Login</h1>
            <div class="head">
                <img src="{% static 'images/user.png' %}" alt=""/>
            </div>
            <form action="" method="POST">
                {% csrf_token %}
                {{ userfrom.username }}
                {{ userfrom.password }}
                <div class="submit"><input type="submit" value="login" ></div>
                <p><a href="#">Forgot Password ?</a></p>
            </form>
        </div>
    </div>
</body>
</html>
```

index.html 的实现如下：

```html
<!DOCTYPE html>
<html lang="{{ LANGUAGE_CODE|default:"en-us" }}">
<head>
    <meta charset="UTF-8">
    <title>首页</title>
</head>
<body>
    <div >
        <h3>欢迎 {{username}} 到来~</h3>
        <div class="submit"><a href="/logout"><input type="submit" value="退出登录" ></a></div>
    </div>
</body>
</html>
```

### 3-5.配置路由
最后一步，在 urls.py 中配置访问路径：

```python
from demo_session.views import login_view, index_view, logout_view

urlpatterns = [
    # demo_session
    path('login/', login_view, name='login'),
    path('index/', index_view, name='index'),
    path('logout/', logout_view, name='index'),
]
```

## 4.实现效果
用户访问 `http://127.0.0.1/login` 进行登录操作。

![进行登录操作](https://img.jikehou.cn/img/20180512_4.png)

当点击 **login** 成功之后，会跳转到首页，首页会显示用户名。

同时，Cookie 中多了一个 **sessionid** 的字段。这字段名就是我们在 setttings.py 定义的。

![这字段名就是我们在 setttings.py 定义的](https://img.jikehou.cn/img/20180512_5.png)

查询数据库 django_session 表的内容，会多出一条数据。

![会多出一条数据](https://img.jikehou.cn/img/20180512_6.png)

表中的字段含义如下：
- session_key: 就是服务器给用户返回的id。在浏览器当中，这个值是保存为sessionid
- session_data: 这是一个加密后的信息，用来保存用户名和密码等信息
- expire_data: 过期时间，Django可以设置过期时间

在新的标签页中打开首页，依然能看到 username 信息。

这证明能自动登录。

![这证明能自动登录](https://img.jikehou.cn/img/20180512_7.png)

如果用户退出登录，再访问首页。

这时会发现看不到了 username 信息了。

![这时会发现看不到了 username 信息了](https://img.jikehou.cn/img/20180512_8.png)

## 5.小结
实现自动登录功能其实不难，只需要在 Django 的 Sessions 组件。

然后根据场景需要，在 settings.py 配置 session 以及 cookie 等信息。

## 6.源码
如果你想获取项目的源码, 点击按钮进行下载。

{% btn 'https://github.com/monkey-soft/Django_demo', 源码下载, far fa-hand-point-right,blue larger %}