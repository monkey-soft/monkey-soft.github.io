---
layout: post
title:  "深入理解HTTP"
date:   2016-11-20 22:35:13
urlname: 1
cover: https://img.jikehou.cn/cover/2016-11-20.jpg
categories: [计算机网络]
tags: []
keywords: [HTTP, HTTP协议, Headers, User-Agent, 计算机网络]
---
## 1.HTTP是什么

> HTTP全称是HyperText Transfer Protocal，即：超文本传输协议。它主要规定了客户端和服务器之间的通信格式。HTTP还是一个基于请求/响应模式的、无状态的协议；即我们通常所说的Request/Response。

<!-- more -->
## 2.HTTP与TCP的关系
TCP协议是位于TCP/IP参考模型中的网络互连层，而HTTP协议属于应用层。因此，HTTP协议是基于TCP协议。

## 3.HTTP请求(HTTP Request)
HTTP请求由三部分组成，分别是：
* 请求行
* HTTP头
* 请求体

下面是请求示例：
```html
GET /?tn=90058352_hao_pg HTTP/1.1
Host: www.hao123.com
Connection: keep-alive
Cache-Control: max-age=0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 6.3; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/50.0.2661.102 UBrowser/5.7.16400.16 Safari/537.36
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.8
```

### 3-1.请求行
同样也是由请求方法（POST/GET）方式、请求的主机、协议版本号三部分组成。下面为请求行的示例：
`GET /?tn=90058352_hao_pg HTTP/1.1`

### 3-2.HTTP头
HTTP头又细分为请求头(request header)、普通头(general header)、实体头(entity header)
而HTTP头主要关注点是其字段

#### 3-2-1.Accept
作用: 浏览器可以接受的媒体类型
例如：  `Accept: text/html`  代表浏览器可以接受服务器回发的类型为 text/html  也就是我们常说的html文档
通配符 * 代表任意类型
例如：  `Accept: */*`  代表浏览器可以处理所有类型,(一般浏览器发给服务器都是发这个)

#### 3-2-2.Accept-Language
作用： 浏览器申明自己接收的语言。 
语言跟字符集的区别：中文是语言，中文有多种字符集，比如big5，gb2312，gbk等等；
例如： `Accept-Language: zh-CN,zh`

#### 3-2-3.Accept-Encoding
作用： 浏览器申明自己接收的编码方法，通常指定压缩方法（gzip，deflate）
例如：`Accept-Encoding: gzip`, `Accept-Encoding: deflate`

#### 3-2-4.User-Agent
作用： 告诉HTTP服务器， 客户端使用的操作系统的名称和版本以及浏览器的名称和版本.
例如： `User-Agent: Mozilla/5.0 (Windows NT 6.3; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/50.0.2661.102 UBrowser/5.7.16400.16 Safari/537.36`

#### 3-2-5.Content-Type
作用： 告诉服务器，请求的内容的类型
常见的字段： 假设使用POST方式请求
* text/xml              [请求体为文本]
* application/json      [请求体为JSON数据]
* application/xml       [请求体为xml数据]
* image/jpeg            [请求体为jpeg图片]
* multipart/form-data   [请求体为表单]

#### 3-2-6.Cookie
作用： 最重要的header，将cookie的值发送给HTTP服务器

#### 3-2-7.Connection
例如：　`Connection: keep-alive`   当一个网页打开完成后，客户端和服务器之间用于传输HTTP数据的TCP连接不会关闭，如果客户端再次访问这个服务器上的网页，会继续使用这一条已经建立的连接
例如：  `Connection: close`  代表一个Request完成后，客户端和服务器之间用于传输HTTP数据的TCP连接会关闭， 当客户端再次发送Request，需要重新建立TCP连接。

#### 3-2-8.Content-Length
作用：发送给HTTP服务器数据的长度。
例如： `Content-Length: 18`

#### 3-2-9.Referer:
作用： 提供了Request的上下文信息的服务器，告诉服务器我是从哪个链接过来的。


### 3-3.请求体
这个只有post方式请求才有，get方式请求没有。

#### 3-3-1.HTTP响应(HTTP Response)
HTTP Response的结构跟Request的结构基本一样。同样分为三部分：
* 响应行
* 响应头
* 响应体

下面是响应示例：
```html
HTTP/1.1 200 OK
Cache-Control: max-age=0
Content-Encoding: gzip
Content-Length: 156474
Content-Type: text/html;charset=UTF-8
Cxy_all: 90058352_hao_pg+d4fa7f28cefb9b120f868558e440bafa
Date: Sun, 20 Nov 2016 05:09:51 GMT
Expires: Sun, 20 Nov 2016 05:09:51 GMT
Lfy: nj02.11
Server: BWS/1.0
Set-Cookie: __bsi=11619936655404239050_00_60_N_R_126_0303_c02f_Y; max-age=3600; domain=www.hao123.com; path=/
```

### 3-4.响应行
响应行由协议版本、响应状态构成
下面为响应行的示例：
`HTTP/1.1 200 OK`

### 3-5.响应头
响应头关注点是字段，常见的字段如下：

#### 3-5-1.Cache-Control
作用: 非常重要的规则。 这个用来指定Response-Request遵循的缓存机制。
例如：
`Cache-Control:Public`      可以被任何缓存所缓存
`Cache-Control:Private`     内容只缓存到私有缓存中
`Cache-Control:no-cache`    所有内容都不会被缓存

#### 3-5-2.Content-Type
作用：服务器告诉浏览器，自己响应的对象的类型和字符集
例如:
`Content-Type: text/html; charset=utf-8`
`Content-Type: image/jpeg`

#### 3-5-3.Expires
作用: 浏览器会在指定过期时间内使用本地缓存
例如: `Expires:Sun, 20 Nov 2016 05:09:51 GMT`

#### 3-5-4.Connection
跟HTTP头中的`Connection`是同样的原理

#### 3-5-5.Content-Encoding
跟HTTP中头的`Content-Encoding`是同样的原理

#### 3-5-6.Content-Length
作用：指明实体正文的长度，以字节方式存储的十进制数字来表示。
例如: `Content-Length: 156474`

#### 3-5-7.Date
作用: 生成消息的具体时间和日期
例如: Date: Sun, 20 Nov 2016 05:09:51 GMT

### 3-6.响应体
响应体包含的内容是网页的内容信息，主要是html代码等