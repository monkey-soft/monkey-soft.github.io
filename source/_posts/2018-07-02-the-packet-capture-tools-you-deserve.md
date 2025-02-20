---
layout: post
title:  "这些抓包工具，你值得拥有"
date:   2018-07-02 22:08:16
urlname: 65
cover: https://img.jikehou.cn/cover/2018-07-02.jpg
categories: [Python爬虫入门]
tags: [Python, 网络爬虫, 抓包工具]
keywords: [Python, 网络爬虫, 抓包工具, 抓包]
---
如今的时代是互联网时代，互联网已经在我们的生活如影随形。

以说我们无时无刻在跟互联网打交道。

而在工作，我们可能会因开发调试、测试、排查网络故障等原因，需要对网路数据包进行抓取、拦截以解析。

因此，本文主要内容是推荐几款不错的抓包工具。
<!-- more -->
## 1.Fiddler
Fiddler 是一个使用 C# 编写的 http 抓包工具。

它使用灵活，功能强大，支持众多的 http 调试任务，是 web、移动应用的开发调试利器。

所以 Fiddler 经常被运用在网络爬虫抓包、HTTP API 测试、手机抓包等场景。

![Fiddler](https://img.jikehou.cn/img/20180702_1.png)

{% btn 'https://www.telerik.com/fiddler', 下载地址, far fa-hand-point-right,blue larger %}


推荐原因：
- 操作简单，上手容易，学习成本低。
- 能够抓取 HTTP/HTTPS 协议的数据包。
- 支持伪造 CA 证书来欺骗浏览器和服务器，从而实现解密 HTTPS 数据包。
- 不仅支持抓取 PC 浏览器的数据包，而且支持抓取手机数据包。
- 支持设置“断点”，从而能够修改 HTTP 的请求头信息以及请求体的数据。


## 2.Charles
Fiddler 虽然强大且好用，但是不支持 Mac OS 系统。

Charles 是 Fiddler 在 Mac 系统上的代替品。目前 Charles 算是 Mac 系统上最好用的抓包工具。

它使用 Java 语言开发的，所以安装以及使用之前，要事先安装好 Java 环境。另外，它还支持 Windows、Linux 等操作系统。

![Charles](https://img.jikehou.cn/img/20180702_2.png)


不过 Charles 是一款收费的软件，当 30 天的免费体验期过后，就需要花费 30 刀购买一个 License。

值得庆幸的是国内有个名为 zzzmode 的大神提供了破解方法。破解版下载地址：

{% btn 'https://www.zzzmode.com/mytools/charles/', 下载地址, far fa-hand-point-right,blue larger %}


推荐原因：
- 同样操作简单，使用方便。
- 支持捕获 HTTP/HTTPS 的数据包
- 支持修改网络请求参数
- 支持截获网络请求并动态修改
- 支持流量控制。可以模拟慢速网络以及等待时间（latency）较长的请求。
- 支持AJAX调试。可以自动将json或xml数据格式化，方便查看。

## 3.Appium
Appium 是移动端自动化测试框架。它跟 Selenium 有点类似，使用驱动程序在 Android、iOS 设备上执行模拟点击、滑动等操作。

![Appium](https://img.jikehou.cn/img/20180702_3.jpg)

Appium 的核心是一个公开 REST API 的 Web Server。

它负责监听来自 Client 的连线与指令，并且把执行结果以 HTTP 状态的方式回应。

目前安装 Appium 可以通过 Appium Desktop来安装。

{% btn 'https://github.com/appium/appium-desktop/releases', 下载地址, far fa-hand-point-right,blue larger %}


推荐原因：
可以使用 Appium 模拟 App 的操作，从而到达爬取 App 加密数据的目的。

## 4.Wireshark
Wireshark 是一款非常优秀、支持 Unix 和 Windows 平台的网络协议分析工具。

它可以监听电脑的网卡所有的数据包，实现实时检测网络通讯数据以及获取详细的封包指令。

它是运维工程师、网络安全工程师的必备工具。

运维工程师可以用其排查网络问题。

网络安全工程师可以利用其监控 TCP 网络动态、分析 DDos 数据等。

![Wireshark](https://img.jikehou.cn/img/20180702_4.png)

{% btn 'https://www.wireshark.org/', 下载地址, far fa-hand-point-right,blue larger %}


推荐原因：
Wireshark 能抓取网卡所有的数据包。这也说明其能抓取 ISO 模型中除了物理层之外的协议数据包。因此，它是我们学习计算机网络知识的好帮手。不过，它功能比较多，需要一定时间成本。