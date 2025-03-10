---
layout: post
title:  "账号认证那些事"
date:   2018-12-20 20:20:28
urlname: 92
cover: https://img.jikehou.cn/cover/2018-12-20.jpg
categories: [计算机网络]
tags: []
keywords: [账号认证, Request, Session, Token, OAuth]
---
QQ 承载着我们一代人的青春和记忆，一个账号和密码就能体验 QQ 各种功能。

而微信作为一款国民级应用，是每个人手机必安装的软件，同样也需要一个账号登陆才能使用。

纵观各种社交应用、网站，往往都是离不开账号体系。

账号体系中有个重要的功能是账号登录，账号登录涉及到身份认证方法。

我们在模拟登录网站去进行数据采集时，经常需要跟网站的身份认证“斗智斗勇”。

因此，让我们来扒一扒其中的一些原理。
<!-- more -->
## 1.基本身份验证

我们写的爬虫都是采用 HTTP/HTTPS 协议。

HTTP 协议中有种名为 Basic Authentication（基本身份验证）的验证方式。

这种认证方式在 HTTP/1.0 就定义了。

它的原理是在请求的 Headers 中增加 `Authorization` 字段，该字段的值是将“`用户名：密码`”的经过 Base64 编码之后的字符串；然后将其发送给服务器端做校验。

我们进一步理解 HTTP 基本认证的过程：
1. 客户端发送 Request 给服务端。
2. 因为 Request 的 Headers 中没有包含 Authorization 字段，服务器会返回一个 401 错误给客户端。
3. 客户端把用户名和密码用 base64 编码之后，放在 Authorization header 中发送给服务器进行验证。
4. 服务端将 Authorization header 中的用户名和密码取出来，进行验证，如果验证通过将根据需求发送资源给客户端。


## 2.基于 Session 的认证

因为 HTTP 协议是无状态的，所以有人提出 Session 机制来维护服务器和用户之间的状态。

如果需要验证身份，那么在服务端生成用户相关的 Session 数据，然后发给客户端 Session_id 存放到 Cookies 中。

客户端下次请求时带上 Session_id 就可以验证服务器端是否存在 Session 数据，以此完成用户认证。

这种 Session 认证是网站平常用的比较多。

这种认证方式，可以更好的在服务端对会话进行控制，安全性比较高(Session_id 随机），但是服务端需要存储 Session 数据(如内存或数据库）。

## 3.基于 Token 的认证

基于 Token（令牌）的认证已经变得更加普遍最近随着 RESTful API 的应用，单页应用程序和微服务的兴起。

怎么理解 token 呢？

当发出HTTP请求时， token 是验证用户是否有资格访问资源的凭证。

基于令牌的认证流程如下：
1. 用户输入账号和密码。
2. 服务器验证信息是否正确；如果正确则返回已签名的 token。
3. token 储在客户端，最常见的是存储在 local storage 中，但也可以存储在 Session 或 Cookies 中。
4. 后面每次 HTTP 请求都在 Header 中带上 token。
5. 服务端收到 token 通过验证后即可确认用户身，并且如果令牌有效，则接受请求。
6. 一旦用户注销，令牌将在客户端被销毁。

基于 token 的用户认证是一种服务端无状态的认证方式，服务端不用存放 token 数据或当前 Session 的记录。

这种方式相对 Cookies 的认证方式就简单一些，服务端不用存储认证数据，易维护扩展性强， token 存在 localStorage 可避免 CSRF ， web 和 app 应用这用接口都比较简单。

不过这种方式在加密或解密的时候会有一些性能开销。

说到基于 Token 的认证，就不得不说下 JWT。

JWT 全称是“JSON Web Token”，它是一种基于 Token 的认证标准。

## 4.OAuth

OAuth 是一种认证协议，允许用户对没有密码的服务器执行认证。 

OAuth 存在很多版本，OAuth 1.0，OAuth 1.0a 和 OAuth 2.0。

OAuth 认证也是很好理解，就是一些网站或者应用支持第三方登录（QQ、微信、微博等）。

## 5.OpenID

OpenId 是另一种不需要密码的身份验证协议（类似于OAuth）。

它们两者的区别是 ：

### OpenID 强调 `验证 Authentication`

例如使用 QQ 方式登录知乎。最开始是要请求认证，用户输入 QQ 号和密码，点击登录。腾讯会先进行验证该用户是否为腾讯 QQ 的用户；如果是我的用户，那么腾讯 QQ 会通知知乎说“刚才登录是我腾讯 QQ 的用户，验证没有问题，可以你知乎社区”，这个过程就是认证（Authentication）。

### OAuth 强调 `授权 Authorization`

微信一开始是支持 QQ 方式登录，登录时微信会乎会询问允许获取你的 QQ 昵称、头像、性别等信息。这个就是授权（Authorization）。