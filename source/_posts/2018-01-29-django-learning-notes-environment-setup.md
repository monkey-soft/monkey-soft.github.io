---
layout: post
title:  "Django 学习笔记之环境搭建"
date:   2018-01-29 11:23:53
urlname: 35
cover: https://img.jikehou.cn/cover/2018-01-29.jpg
categories: [Python编程]
tags: [Python, Web开发]
keywords: [Python, web, Django]
---
古人云：功遇善其事，必先利其器。在正式学习 Django Web 框架之前，我们要把准备工作做好。

准备工作主要是搭建开发环境，具体工作是安装 Python、创建虚拟环境 venv、安装 Django、安装 IDE 工具（Pycharm）。
<!-- more -->
## 1.安装 Python
如果你使用的桌面系统是 Windows，你需要到 Python 官网下载安装包。

Linux 和 Mac 系统都自带了 Python 运行环境。Python 分为 2 和 3 版本，目前 Python 团队即将停止维护 Python 2 版本，所以建议安装 Python 3。

目前最新版本是 3.6.4。另外，本系列文章适合具备 Python 基础的同学。

如果你对 Python 基本语法还是很懵懂，建议你先把基础知识夯实。

## 2.创建虚拟环境 venv
一提到 Python 虚拟环境，你会惊叹说为什么不用 virtualenv？

如果你生产或开发环境需同时支持 Python 2 和 Python 3 ，那就需要 virtualenv。

我们是从零开始学习 Django，所以可以直接使用 venv。简单来说，venv 模块是 Python 3.3 版本之后，标准库自带的虚拟环境创建和管理工具，在 Python 3 版本是代替 virtualenv。

**为什么要创建虚拟环境呢？**

你或许会从网上下载一些安全工具或者软件。

你害怕这些程序带有后门，甚至是木马程序。所以不敢在自己当前的系统中直接运行。

你会使用 VMware 创建一个虚拟机，然后在虚拟机中运行该程序。不管程序是否是病毒，都不会对我当前的系统造成影响。

因为虚拟机和当前的系统相互隔离，互不影响。

虚拟机出现问题，只要删除即可，不会影响到当前系统。

使用 venv 创建虚拟环境也是同样的道理。

在当前系统中创建出一个环境，该环境可以跟当前系统互不影响，你可以随意折腾。

另外，有了 virtualenv 虚拟环境之后，我们就可以把那个文件夹整体拷贝了，部署起来方便很多。

### 2-1.venv 使用
创建 Python 虚拟环境，其实是“创建” 一个文件夹。

假如我们需要在 D 盘中创建一个名为 web_dev 的虚拟环境。

打开终端，执行以下命令。

```shell
// venv 后面接上创建虚拟环境的绝对路径，建议文件名不要事先存在。
// Windows、Mac、Linux 执行命令都是一样，只不过路径不一样
python -m venv D://web_dev
```

执行创建命令之后，你会发现多出了一个名为 web_dev 文件夹，这说明已经创建成功。进入目录，里面有四个文件夹。
![web_dev 文件夹](https://img.jikehou.cn/img/20180129_1.png)

创建虚拟环境的完成，只是完成了一半工作。革命还尚未成功，我们还需要激活虚拟环境。

依然是打开终端，进入 `Scripts` 文件夹，运行 `activate.bat` 来激活虚拟环境。 

Linux 下没有 `Scripts` 这个目录，取而代之的是 `bin`目录。

而激活脚本名则是`activate`。

![激活脚本](https://img.jikehou.cn/img/20180129_2.png)

激活完毕，我们下一步就是安装 Django 库。

## 3.安装 Django
还是上述的虚拟环境中，我们通过 pip 方式来安装 Django。

如果你把终端关闭了，这也意味着把虚拟环境给关闭了。你按照上述激活步骤重新进入虚拟环境即可。
```python
D:\web_dev>D://web_dev/Scripts/activate.bat
(web_dev) D:\web_dev>pip install django
Collecting django
```

## 4.安装 IDE 工具（Pycharm）
我们需要到 Pycharm 官网下载安装包。

安装版本一定要选择 Professional ！

安装版本一定要选择 Professional ！

安装版本一定要选择 Professional ！

重要的话说三遍~

因为这个版本集成了很多 Web 开发组件，无须手动。

另附上：

{% btn 'https://www.jetbrains.com/pycharm/download/', Pycharm下载地址, far fa-hand-point-right,blue larger %}

Pycharm 是收费版本。如果你有条件的话，可以选择购买正版。或者到网上选择激活码。
