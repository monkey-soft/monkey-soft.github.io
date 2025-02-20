---
layout: post
title:  "pustil-获取系统信息库"
date:   2018-01-09 17:38:18
urlname: 31
cover: https://img.jikehou.cn/cover/2018-01-09.jpg
categories: [小白入门Python]
tags: [Python]
keywords: [Python, pustil, 系统信息]
---
运维工程师经常使用 Python 编写脚本程序来做监控系统运行的状态。

如果自己手动使用 Python 的标准库执行系统命令来获取信息，会显得非常麻烦。既要兼容不同操作系统，又要自己处理解析信息。

为了解决的痛点问题，psutil 就横空出世。

它的出现无疑是运维工程师的福音。

运维小伙伴通过它执行一两行代码即可实现系统监控。
<!-- more -->
## 1.简介
`psutil`全称是`process and system utilities`。

psutil 是一个跨平台的应用于系统监控、分析、以及对系统进程进行一定管理的 Python 第三方库。

它不仅能够轻松获取系统中正常运行的进程和系统利用率（例如 CPU、内存、磁盘、网络等）信息，还实现了跟 UNIX 系统命令行工具类似的功能。

可以说是运维工作的“必备品”。

它功能强大，操作简单。这也促使很多开源项目都集成它到自己项目中，不妨有谷歌的 GRR 项目、脸书的 osquery 项目等。

{% btn 'https://github.com/giampaolo/psutil', github 地址, far fa-hand-point-right,blue larger %}


## 2.安装
安装 psutil 是有多种办法：通过 pip 安装，通过源码方式安装，通过下载 tar 压缩包来安装。其中通过 pip 的方式是最简单的。

```python
pip install psutil
# 如果出现因下载失败导致安装不上的情况，建议使用代理
pip --proxy http://代理ip:端口 install psutil
```

## 3.使用
前面说到 psutil 能监听到 CPU、内存、磁盘、网络、传感器、进程等，现在跟着我来学习下。
### 3-1.获取 CPU 信息

1）我先获取自己电脑 CPU 的核心数，我电脑的 CPU 型号是 I5 4590。我通过搜索引擎得知该型号 CPU 是四核四线。
```python
import psutil

psutil.cpu_count()  # 获取 CPU 的逻辑核心数，默认logical=True
psutil.cpu_count(logical=False)    # 获取 CPU 的物理核心数

>> 4
>> 4
# 这说明该 CPU 型号是真四核。
```

2）统计 CPU 的时间：
```python
import psutil
psutil.cpu_times() 

>> scputimes(user=9276.365234375, system=5034.5390625, idle=96077.0703125, interrupt=181.78796863555908, dpc=298.227108001709)
```
**cpu_times()** 返回的是带有系统所有逻辑  CPU 运行时间的元组，单位是秒。返回元组的字段中有这几个常用字段：
- user：执行用户进程的时间，Linux 系统还包括访客的时间
- system：执行内核进程时间
- idle：闲置时间
- iowait（Linux 特有）：等待 I/O 操作的时间
- irp（Linux 特有）：打断服务硬件的时间
- interrupt（Windows 特有）：跟 irp 字段类似
- dpc（Windows 特有）：服务延迟程序调用（DPCs）的时间

如果增加参数 `percpu=True`， **cpu_times()** 会以列表的形式输出每个逻辑 CPU 的时间。

3）获取当前 CPU 的利用率的百分比：
```python
import psutil

psutil.cpu_percent()
>> 16.5
```
**cpu_percent()** 默认的参数是 `interval=None, percpu=False`。当 **interval** 为0或者None时，表示的是 interval 时间内的sys的利用率。当 **percpu** 为 False 表示所有逻辑 CPU 的使用率。

如果你想统计 15 秒中内，间隔 5 秒每个逻辑 CPU 的使用率，你可以这么做：
```python
import psutil

for i in range(3):
    psutil.cpu_percent(interval=5, percpu=True)

>>  [9.1, 7.2, 7.2, 6.2]
>>  [5.9, 3.8, 9.0, 8.4]
>>  [12.3, 8.4, 3.4, 4.1]
```

4）获取 CPU 频率信息：

```python
import psutil

psutil.cpu_freq()
>> [scpufreq(current=931.42925, min=0.0, max=3301.0)]
```
可知 **cpu_freq()** 返回的带有所有逻辑 CPU 频率的元组，包括当前、最小和最大频率。

### 3-2.获取内存信息
1）获取物理内存信息：
```python
import psutil

psutil.virtual_memory()
>>  svmem(total=8509177856, 
available=1692307456, percent=80.1, used=6816870400, free=1692307456)
```
**virtual_memory()** 返回一个记载当前电脑设备中可用的物理内存信息的元组，单位是字节。从返回结果得知，当前内存总大小为 `8509177856 Byte = 8 GB`，可用内存（闲置内存） `1692307456 Byte = 1.6 GB`，当前内存使用率为 `80.1%`。值得注意的是，**内存总大小不等于 Used 和 available 两者的总和**

**available** 字段在 Linux 系统下，计算方式则不同。available = free + buffers +cached。`buffers` 指的是 Linux 系统下的 Buffers 内存, 表示块设备(block device)所占用的缓存页; 而 `cached` 指的是 Linux 系统下的 Cache 内存，顾名思义为高速缓存。


2）获取交换内存信息：
```python
import psutil

psutil.swap_memory()
>>  sswap(total=17016451072, used=7407996928, free=9608454144, percent=43.5, sin=0, sout=0)
```
**swap_memory()** 获取的是系统的交换内存的信息，也就是我们常说的虚拟内存。前面四个字段跟物理内存含义一样。而 `sin` 表示从磁盘调入时 swap 的大小， `sout` 表示从swap调出到 disk 的大小。这两个字段在 Windows 系统下是没有意义。因此，获取结果为 0。


### 3-3.获取磁盘信息
1）获取磁盘分区信息：
```python
import psutil

psutil.disk_partitions()

>>  
[sdiskpart(device='C:\\', mountpoint='C:\\', fstype='NTFS', opts='rw,fixed'), 
sdiskpart(device='D:\\', mountpoint='D:\\', fstype='NTFS', opts='rw,fixed'), 
sdiskpart(device='E:\\', mountpoint='E:\\', fstype='NTFS', opts='rw,fixed'), 
sdiskpart(device='F:\\', mountpoint='F:\\', fstype='NTFS', opts='rw,fixed')]
```
**disk_partitions(all=False)** 返回所有挂载磁盘分区信息的列表。有点类似 Linux 的 `df` 命令。各个字段的含义分别为：
- device：分区
- mountpoint：挂载点
- fstype：文件系统格式
- opts：挂载参数

大部分人都对 Windows 系统的分区信息了解比较多，对 Linux 系统所知甚少。因此，我给出虚拟机 Ubuntu 系统的磁盘信息, 方便大家学习。
```python
[sdiskpart(device='/dev/sda1', mountpoint='/', fstype='ext4', opts='rw,errors=remount-ro'),
 sdiskpart(device='/dev/sda2', mountpoint='', fstype='swap', opts='rw')]
```

2）获取磁盘使用率：
**disk_usage()** 统计参数中路径目录的磁盘使用情况。

它需要传入一个路径参数；我传入的参数是 "/"，意味着获取当前整个硬盘的使用率。
```
import psutil

psutil.disk_usage('/')

>>  sdiskusage(total=128033574912, used=81997942784, free=46035632128, percent=64.0)
```

3）获取磁盘 IO 信息：
```
import psutil

psutil.disk_io_counters()

>>  sdiskio(read_count=510749, write_count=505110, read_bytes=13353246720, write_bytes=8962015232, read_time=275, write_time=238)
```
**disk_io_counters()** 获取当前磁盘的 I/O 数据信息情况，也就是读取和写入信息。

### 3-4.获取网络信息
1）获取整个系统的网络信息
```python
import psutil

psutil.disk_io_counters()

>>  snetio(bytes_sent=77587966, bytes_recv=1113555204, packets_sent=500638, packets_recv=1048467, errin=0, errout=0, dropin=0, dropout=0)
```
**disk_io_counters()** 返回整个系统所有网卡（包括有线网卡、无限网卡）的进行网络读写数据、发包数等信息。个人认为可以使用该方法来抓包。各个字段含义如下：
- bytes_sent：发送的字节数
- bytes_recv：收的字节数
- packets_sent：发送到数据包的个数
- packets_recv：接受的数据包的个数
- errout：发送数据包错误的总数
- dropin：接收时丢弃的数据包的总数
- dropout：发送时丢弃的数据包的总数( OSX 和 BSD 系统总是 0 )

如果增加参数 `pernic=True`，disk_io_counters() 则会分别输出各个网卡的网络信息数据。

2）获取当前网络连接信息
**net_connections()** 的作用跟系统命令 `netstat -an` 是一样的。输出当前系统中所有类型的网络连接数据。
```python
import psutil

psutil.net_connections()
# 数据过多，我就不打印输出结果。
```

3）获取网络接口信息
我们能通过 **net_if_addrs()** 函数来获取到各个网卡的 IP 地址、网关等信息
```python
import psutil
psutil.net_if_addrs()
```

4）获取网络接口状态
**net_if_stats()** 获取的是各个网卡的状态信息，例如网卡是否处于激活状态、当前网速等
```python
import psutil
psutil.net_if_stats()
```

### 3-5.获取系统相关信息
1) 获取当前登录用户信息
```python
import psutil

psutil.users()
>>  [suser(name='monkey', terminal=None, host='0.125.2.117', started=1515585444.0, p
id=None)]
```
**users()** 是返回当前登录用户的信息。例如用户的名称 `name`、运行的终端 `terminal`， 在 Windows 系统下就是我们常说的 CMD 窗口、登录的 IP 地址 `host`、登录的时长 `started`以及登录的进程 `pid`，在 Windows 和 OpenBSD 系统中，该字段为 None。

2) 获取系统启动时间
`psutil.boot_time()` 获取的是系统启动的时间点，而不会启动消耗时长。


### 3-6.获取进程信息
如果查看当前系统的所有进程信息，你可以使用 **test()** 方法。跟 Windows 系统下的 `tasklist` 命令作用类似。
```python
import psutil
psutil.test()
```

psutil 还提供一列的方法来查看系统进程的信息。

```python
import psutil

psutil.pids()    # 列出所有进程的PID
>> [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 32, 37, 38, 40, 41, 71, 199, 203, 218, 219, 220, 325, 327, 344, 345, 364, 417, 569, 764, 765, 817, 1042, 1058, 1070, 1110, 1186, 1194, 1197, 1209, 1211, 1213, 1215, 1217, 1219, 1228, 1229, 1231, 1298, 1643, 1745, 1794, 1796, 1829, 1846, 2004, 2039, 2154, 2175]

p = psutil.Process(2154)  # 实例化一个Process对象，参数为一个进程的PID
p.name()   # 获取进程名
>> 'python'

p.exe()    # 获取进程 bin 路径, 即安装路径
>>'/usr/bin/python'

p.cwd()    # 获取进程的工作目录
>> '/root'

p.status()   # 获取进程的运行状态
>>'running'

p.create_time()    # 获取进程创建时间点
>> 1427469528.49

p.cpu_times()    # 获取进程使用 CPU 时间信息
>> pcputimes(user=0.081150144, system=0.053269812, children_user=0.0, children_system=0.0)

p.memory_info() # 获取进程使用的内存
>> pmem(rss=8310784, vms=2481725440, pfaults=3207, pageins=18)

p.open_files() # 获取进程打开的文件
>> []

p.connections() # 获取进程相关网络连接
>> []

p.num_threads() # 获取进程的线程数量
>> 1
```