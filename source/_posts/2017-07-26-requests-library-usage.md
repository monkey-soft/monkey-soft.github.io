---
layout: post
title:  "详解 Requests 库的用法"
date:   2017-07-26 16:08:04 +0800
urlname: 11
cover: https://cdn.jsdelivr.net/gh/monkey-soft/img@master/cover/2017-07-26.jpg
categories: [Python爬虫入门]
tags: [Python, 网络爬虫, Requests]
keywords: [Python, 网络爬虫, Requests]
---
如果你把上篇多线程和多进程的文章搞定了，那么要恭喜你了 。

你编写爬虫的能力上了一个崭新的台阶。

不过，我们还不能沾沾自喜，因为任重而道远。

那么接下来就关注下本文的主要内容。

本文主要介绍 urllib 库的代替品 —— `Requests`。
<!-- more -->

## 1.Requests 简介
引用 Requests 官网的说明：

> Requests is the only Non-GMO HTTP library for Python, safe for human consumption.

Requests 官方的介绍语是多么霸气。之所以能够这么霸气，是因为 Requests 相比 urllib 在使用方面上会让开发者感到更加人性化、更加简洁、更加舒服。

并且国外一些知名的公司也在使用该库，例如 Google、Microsoft、Amazon、Twitter 等。

因此，我们就更加有必要来学习 Request 库了。

在学习之前，我们来看下它究竟具有哪些特性？

具体如下：
- Keep-Alive & 连接池
- 国际化域名和 URL
- 带持久 Cookie 的会话
- 浏览器式的 SSL 认证
- 自动内容解码
- 基本/摘要式的身份认证
- 优雅的 key/value Cookie
- 自动解压
- Unicode 响应体
- HTTP(S) 代理支持
- 文件分块上传
- 流下载
- 连接超时
- 分块请求
- 支持 .netrc

## 2.安装 Requests
古人云：“工欲善其事，必先利其器”。在学习 Requests 之前，我们应先将库安装好。安装它有两种办法。

### 方法1：通过 pip 安装

比较推荐使用这种方式，既简单又方便管理。
```python
pip install requests
# 如果出现因下载失败导致安装不上的情况，可以先启动 ss 再执行安装命令
# 或者在终端中使用代理
pip --proxy http://代理ip:端口 install requests
```

### 方法2：通过源码安装

先通过 git 克隆源码库：
```python
git clone git://github.com/kennethreitz/requests.git
```
或者直接到 [github](https://github.com/requests/requests) 网页上下载源码压缩包

接着进入到 requests 目录中执行以下命令：
```python
python setup.py install
```

## 3.发起请求
有了前面学习 urllib 库的经验，现在我们学习 Requests 应该会更加容易上手。

### 3-1.简单抓取网页
我们使用 Requests  向百度贴吧发起一个 HTTP 请求，并获取到它页面的源代码。

```python
import requests

# 使用 get 方式请求
response = requests.get('https://tieba.baidu.com/')
print(response.text)
```

那么使用 POST 请求网页，代码又该怎么写呢？相信答案已经浮现在你脑海中了。没错，就是将 get 换成 post 即可。
```python
import requests

# 使用 post 方式请求
response = requests.post('https://tieba.baidu.com/')
print(response.text)
```


### 3-2.传递 URL 参数
我们在请求网页时，经常需要携带一些参数。Requests 提供了`params`关键字参数来满足我们的需求。

params 是一个字符串字典，我们只要将字典构建并赋给 params 即可。

我们也无须关心参数的编码问题，因为 Requests 很人性化，会将我们需要传递的参数正确编码。

它的具体用法如下：

```python
import requests

url = 'http://httpbin.org/get'
params = {'name': 'Numb', 'author': 'Linkin Park'}
# params 支持列表作为值
# params = {'name': 'Good Time', 'author': ['Owl City', 'Carly Rae Jepsen']}
response = requests.get(url, params=params)
print(response.url)
print(response.text)
```

如果字典为空是不会被拼接到 URL中的。另外，params 的拼接顺序是随机的，而不是写在前面就优先拼接。

```python
#运行结果如下：
http://httpbin.org/get?name=Numb&author=Linkin+Park
http://httpbin.org/get?name=Good+Time&author=Owl+City&author=Carly+Rae+Jepsen
```
你也许会疑问，为什么会有多了个"+"号呢？这个是 Requests 为了替代空格，它在请求时会自动转化为空格的。


### 3-3.构造请求头

为了将 Requests 发起的 HTTP 请求伪装成浏览器，我们通常是使用`headers`关键字参数。headers 参数同样也是一个字典类型。
```python
import requests

url = 'https://tieba.baidu.com/'
headers = {
    'user-agent': 'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36'
    # 还可以设置其他字段。
}
response = requests.get(url, headers=headers)
print(response.url)
print(response.text)
```
具体用法见以下代码：


#### 3-3-1.使用 data 参数提交数据
data 参数通常结合 POST 请求方式一起使用。如果我们需要用 POST 方式提交表单数据或者JSON数据，我们只需要传递一个字典给 data 参数。


#### 3-3-2.提交表单数据
我们使用测试网页`http://httpbin.org/post`来提交表单数据作为例子进行展示。

```python
import requests

url = 'http://httpbin.org/post'
data = {
    'user': 'admin',
    'pass': 'admin'
}
response = requests.post(url, data=data) 
print(response.text)
```
```
运行结果如下：我们会看到`http://httpbin.org/post`页面打印我们的请求内容中，有`form`字段。

```python
{
  "args": {}, 
  "data": "", 
  "files": {}, 
  "form": {
    "pass": "admin", 
    "user": "admin"
  }, 
  "headers": {
    "Accept": "*/*", 
    "Accept-Encoding": "gzip, deflate", 
    "Cache-Control": "max-age=259200", 
    "Connection": "close", 
    "Content-Length": "21", 
    "Content-Type": "application/x-www-form-urlencoded", 
    "Host": "httpbin.org", 
    "User-Agent": "python-requests/2.11.1"
  }, 
  "json": null, 
  "origin": "14.*.*.*", 
  "url": "http://httpbin.org/post"
}
```

#### 3-3-3.提交 JSON 数据

在HTTP 请求中，JSON 数据是被当作字符串文本。所以，我们使用 data 参数的传递 JSON 数据时，需要将其转为为字符串。我们继续使用上文的代码做演示。

```python
import json
import requests

url = 'http://httpbin.org/post'
data = {
    'user': 'admin',
    'pass': 'admin'
}
response = requests.post(url, data=json.dumps(data))
print(response.text)
```

你可以拿下面的运行结果和提交表单数据的运行结果做下对比，你会了解更加清楚两者的差异。
```python
{
  "args": {}, 
  "data": "{\"pass\": \"admin\", \"user\": \"admin\"}", 
  "files": {}, 
  "form": {}, 
  "headers": {
    "Accept": "*/*", 
    "Accept-Encoding": "gzip, deflate", 
    "Cache-Control": "max-age=259200", 
    "Connection": "close", 
    "Content-Length": "34", 
    "Host": "httpbin.org", 
    "User-Agent": "python-requests/2.11.1"
  }, 
  "json": {
    "pass": "admin", 
    "user": "admin"
  }, 
  "origin": "14.*.*.*", 
  "url": "http://httpbin.org/post"
}
```

那是否有更加简便的方法来传递 JSON 数据？Requests 在 2.4.2 版本新增该功能。我们可以使用 json 参数直接传递，然后它会被自动编码。

```python
import requests

url = 'http://httpbin.org/post'
data = {
    'user': 'admin',
    'pass': 'admin'
}
response = requests.post(url, json=data)
print(response.text)
```

### 3-4.使用代理
有些网站做了浏览频率限制。如果我们请求该网站频率过高，该网站会被封掉我们的 IP，禁止我们的访问。

所以我们需要使用代理来突破这“枷锁”。

这里需要用到`proxies`参数，proxies 也是一个字典类型。具体用法如下：

```python
import requests

url = 'https://tieba.baidu.com/'
proxies = {
    'http':"web-proxy.oa.com:8080",
    'https':"web-proxy.oa.com:8080"
    # 若你的代理需要使用HTTP Basic Auth，可以使用 http://user:password@host/ 语法：
    # "http": "http://user:pass@27.154.181.34:43353/"
}
response = requests.get(url, proxies=proxies)
print(response.url)
print(response.text)
```

除了支持 HTTP 代理，Requests 在 2.10 版本新增支持 SOCKS 协议的代理。

也就是说，Requests 能够使用 ShadowSocks 代理。看到这里，你的内心是不是有点小激动？

使用 SOCKS 代理，需要额外安装一个第三方库，我们就使用 pip 来安装。
```python
pip install requests[socks]
```

安装成功之后，就可以正常使用了，用法跟 HTTP 代理相关。具体见代码：

```python
proxies = {
    'http': 'socks5://user:pass@host:port',
    'https': 'socks5://user:pass@host:port'
}
```


### 3-5.设置请求超时

我们使用代理发起请求，经常会碰到因代理失效导致请求失败的情况。

因此，我们对请求超时做下设置。当发现请求超时，更换代理再重连。

```python
response = requests.get(url, timeout=3)
```
如果你要同时设置 connect 和 read 的超时时间，可以传入一个元组进行设置。
```python
response = requests.get(url, timeout=(3, 30))
```

### 3-6.使用 Cookie

想在响应结果中获取 cookie 的一些值，可以直接访问。
```python
response.cookies['key']  # key 为 Cookie 字典中键
```

想发送 cookies 到服务器，可以使用`cookies`参数。同样该参数也是字典类型
```python
url = 'http://httpbin.org/cookies'
# cookies = dict(domain='httpbin.org')
cookies = {
    'domain':'httpbin.org',
}
response = requests.get(url, cookies=cookies)
print(response.text)
```

## 4.响应结果

我们跟Python 打交道，摆脱不了编码的问题。使用 Requests 请求，我们无需担心编码问题。

感觉 Requests 真的是太人性化了。

请求发出后，Requests 会基于 HTTP 头部对响应的编码作出有根据的推测。

当你访问 response .text 之时，Requests 会使用其推测的文本编码。

```python
response = requests.get(url)
print(response.text)
```

如果你想改变 response 的编码格式，可以这么做：
```python
response.encoding = 'UTF-8' 
```

### 4-1.二进制响应内容
对于非文本请求， 我们能以字节的方式访问请求响应体。Requests 会自动为我们解码 gzip 和 deflate 传输编码的响应数据。
例如，以请求返回的二进制数据创建一张图片，你可以使用如下代码：
```python
from PIL import Image
from io import BytesIO

i = Image.open(BytesIO(response.content))
```

### 4-2.JSON 响应内容

Requests 中也有一个内置的 JSON 解码器，助我们处理 JSON 数据：
```python
import requests

url = 'https://github.com/timeline.json'
response = requests.get(url)
print(response.json())
```
如果 JSON 解码失败， response .json() 就会抛出一个异常。例如，响应内容是 401 (Unauthorized)，尝试访问 response .json() 将会抛出 ValueError: No JSON object could be decoded 异常。

### 4-3.响应状态码
我们需要根据响应码来判断请求的结果，具体是这样获取状态码：
```python
response.status_code
```

Requests 内部提供了一个状态表，如果有需要对状态码进行判断，可以看下`requests.codes`的源码。


## 5.高级用法

### 5-1.重定向与请求历史
有些页面会做一些重定向的处理。Requests 又发挥人性化的特性。

它在默认情况下，会帮我们自动处理所有重定向，包括 301 和 302 两种状态码。

我们可以使用`response .history`来追踪重定向。

`Response.history`是一个 Response 对象的列表，为了完成请求而创建了这些对象。这个对象列表按照从最老到最近的请求进行排序。

如果我们要禁用重定向处理，可以使用`allow_redirects `参数：
```python
response = requests.get(url, allow_redirects=False)
```

### 5-2.会话
Requests 支持 session 来跟踪用户的连接。例如我们要来跨请求保持一些 cookie，我们可以这么做：
```python
s = requests.Session()

s.get('http://httpbin.org/cookies/set/sessioncookie/123456789')
r = s.get("http://httpbin.org/cookies")

print(r.text)
# '{"cookies": {"sessioncookie": "123456789"}}'
```

### 5-3.身份认证
有些 web 站点都需要身份认证成功之后才能访问。urllib 具备这样的功能，Requests 也不例外。

Requests 支持基本身份认证（HTTP Basic Auth）、netrc 认证、摘要式身份认证、OAuth 1 认证等。

#### 5-3-1.基本身份认证

许多要求身份认证的web服务都接受 HTTP Basic Auth。这是最简单的一种身份认证，并且 Requests 对这种认证方式的支持是直接开箱即可用。HTTP Basic Auth 用法如下：

```python
from requests.auth import HTTPBasicAuth

url = 'http://httpbin.org/basic-auth/user/passwd'
requests.get(url, auth=HTTPBasicAuth('user', 'pass'))
# 简写的使用方式
requests.get(url, auth=('user', 'pass'))
```

#### 5-3-2.摘要式身份认证

摘要式是 HTTP 1.1 必需的第二种身份验证机制。这种身份验证由用户名和密码组成。随后将用 MD5（一种单向哈希算法）对摘要式身份验证进行哈希运算，并将其发送到服务器。具体用法如下：

```python
from requests.auth import HTTPDigestAuth

url = 'http://httpbin.org/digest-auth/auth/user/passwd/MD5'
requests.get(url, auth=HTTPDigestAuth('user', 'pass'))
```

#### 5-3-3.OAuth 认证

OAuth（开放授权）认证在我们的生活中随处可见。Requests 同样也支持这中认证方式，其中包括 OAuth 1.0 和 OAuth 2.0。如果你需要用到该认证，你需要安装一个支持库`requests-oauthlib `。我以 OAuth 1.0 认证作为例子进行讲解：

```python
import requests
from requests_oauthlib import OAuth1

url = 'https://api.twitter.com/1.1/account/verify_credentials.json'
auth = OAuth1('YOUR_APP_KEY', 
              'YOUR_APP_SECRET',
              'USER_OAUTH_TOKEN', 
              'USER_OAUTH_TOKEN_SECRET')
requests.get(url, auth=auth)
```
