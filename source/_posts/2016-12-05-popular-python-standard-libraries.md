---
layout: post
title:  "常用 Python 标准库"
date:   2016-12-05 03:47:47 +0800
urlname: 3
cover: https://cdn.jsdelivr.net/gh/monkey-soft/img@master/cover/2016-12-05.jpg
categories: [小白入门Python]
tags: [Python]
keywords: [Python, Python 标准库，re, csv, time, io, thread, json, datetime]
---
众所周知，Python有庞大的库资源，有官方标准库以及第三方的扩展库。

每个库都一把利器，能帮助我们快速处理某方面的问题。

作为一名python的初学者，当把基本的语法、列表和元组、字典、迭代器、异常处理、I/O操作、抽象等知识点学完之后。

我建议把官方常用的标准库也随便学下来。讲真的，你知道这些库之后，你会有种相见恨晚的感觉。

接下来带大家走进python标准库的世界。PS： 使用Python的版本为Python3
<!-- more -->
#### 1.字符串 
* `re`: 正则表达式。用来判断字符串是否是你指定的特定字符串。在爬虫项目中，经常能捕获到它的身影。
* `StringIO`: 提供以文件为保存形式来读和写字符串。还有个性能更加好的`cStringIO`版本
* `struct`: 以二进制字节序列来解释字符串。可以通过格式化参数，指定类型、长度、字节序（大小端）、内存对齐等。

#### 2.数据类型
* `bisect`: 数组二分算法。提供支持按顺序对列表进行排序，而不必每次在列表中插入后再去排序。
* `heapq`: 堆队列算法。最小堆：完全平衡二叉树， 所有节点都小于字节点。
* `datetime`: 提供操作日期和时间的类。其中有两种日期和时间类型： *naive*和*aware* 
* `collections`: 高性能容器数据类型。实现了Python的通用内置容器、字典、列表、集合，和元组专门的数据类型提供替代品
* `pprint`: 提供"整洁低打印"任意Python数据结构的能力。

#### 3.数学运算
* `random`: 各种分布的伪随机数的生成器
* `math`: 数学函数。提供了由C标准的数学函数访问。该库的函数不适用于复数。
* `cmath`: 为复数提供的数学函数。
* `operator`: 提供了重载操作符

#### 4.文件和目录
* `os.path`: 常用路径名操作。提供了操作路径名的常用的函数。
* `filecmp`: 文件和目录的比较。提供了比较文件和目录的函数。
* `shutil`: 高级的文件操作。提供了许多文件和文件集上的操作操作。尤其是提供支持文件复制和删除的函数。

#### 5.数据存储
* `serialization`: Python专用的序列化算法，通常不建议用来存储自定义数据。
* `pickle`: Python对象序列化。提供了一个基本但功能强大的Python对象序列化和反序列化算法。
* `cPickle`: 比`pickle`快1000倍的对象序列化库， 和`pickle`可互相替换。
* `shevle`: 将对象pickle序列化，然后保存到*anydbm*格式文件。*anydbm*是KV结构的数据库，可以保存多个序列化对象。
* `sqlite3`: SQLite数据库DB-API 2.0接口。

#### 6.数据压缩
* `zipfile`: 提供了ZIP文件个创建、读取、写入、最佳和列出zip文件的函数。
* `tarfile`: 提供了tar文件的压缩和解压的函数。

#### 7.文件格式
* `csv`: 提供对CSV文件的读取和写入的函数。

#### 8.加密
* `hashlib`: 安全哈希和消息摘要。实现了一个通用的接口来实现多个不同的安全哈希和消息摘要算法。包括 FIPS 安全哈希算法 SHA1、SHA224、SHA256、SHA384和 SHA512（定义在 FIPS 180-2），以及 RSA 的 MD5 算法（在互联网 RFC 1321中定义)。
* `hmac`: 用于消息认证的加密哈希算法。实现了RFC 2104 中描述的HMAC 算法。
* `md5`: 实现了MD5加密算法。
* `sha`: 实现了sha1加密算法。

#### 9.操作系统
* `time`: 时间获取和转换。提供了各种与时间相关的函数。
* `argparse`: 命令行选项、参数和子命令的解析器。使用该库使得编码用户友好的命令行接口非常容易。取代了之前的`optparse`
* `io`: 提供接口处理IO流。
* `logging`: Python的日志工具。提供了日志记录的API。
* `logging.config`: Python日志配置。用于配置日志模块的API。
* `os`: 提供丰富的雨MAC，NT，Posix等操作系统进行交互的能力。这个模块允许程序独立的于操作系统环境。文件系统，用户数据库和权限进行交互。
* `_thread`: 多线程控制。提供了一个底层、原始的操作 —— 多个控制线程共享全局数据空间。
* `threading`: 高级线程接口。是基于`_thread`模块的，但是比`_thread`更加容易使用、更高层次的线程API。
* `sys`: 提供访问和维护python解释器的能力。这包括了提示信息，版本，整数的最大值，可用模块，路径钩子，标准错误，标准输入输出的定位和解释器调用的命令行参数。

#### 10.进程通信
* `subprocess`: 管理子进程。允许用户产生新的进程，然后连接他们的输入/输出/错误/管道，并获取返回值。
* `socket`: 底层网络接口。
* `signal`: 设置异步时间处理handlers。信号是软中断，提供了一种异步事件通知机制。

#### 11.网络数据处理
* `json`: JSON格式的编码器和解码器。
* `base64`: 提供依据RFC 3548的规定（Base16, Base32, Base64 ）进行数据编码和解码。
* `htmllib`: 提供了一个HTML语法解析器。
* `mimetypes`: 提供了判断给定的URL的MIME类型。

#### 12.操作因特网网络协议
* `urllib`: 提供了用于获取万维网数据的高层接口。这个是Python2.7版本的，Python3已经将其拆分成多个模块`urllib.request`，`urllib.parse`和`urllib.error`。
* `urlparse`: 提供了用于处理URL的函数，可以在URL和平台特定的文件名间相互转换。
* `http.client`: HTTP协议客户端。
* `telnetlib`: 提供了实现Telnet协议的Telnet类。
* `poplib`: POP3协议客户端。
* `ftplib`: FTP协议客户端。
* `smtplib`: SMTP协议客户端。
* `webbrowser`: 提供控制浏览器行为的函数。