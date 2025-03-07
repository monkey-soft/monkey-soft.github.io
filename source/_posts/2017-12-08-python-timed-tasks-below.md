---
layout: post
title:  "Python 定时任务(下)"
date:   2017-12-08 19:45:35 +0800
urlname: 27
cover: https://img.jikehou.cn/cover/2017-12-08.jpg
categories: [Python编程]
tags: [Python, 定时任务]
keywords: [Python, datetime, time, APScheduler]
description: Python 定时任务(下)
---
上篇文章，我们了解到有三种办法能实现定时任务，但是都无法做到循环执行定时任务。

因此，需要一个能够担当此重任的库。它就是`APScheduler`。
<!-- more -->
## 1.简介
`APScheduler`的全称是`Advanced Python Scheduler`。

它是一个轻量级的 Python 定时任务调度框架。

APScheduler 支持三种调度任务：`固定时间间隔`，`固定时间点（日期）`，`Linux 下的 Crontab 命令`。同时，它还支持异步执行、后台执行调度任务。


## 2.安装
使用 pip 包管理工具安装 APScheduler 是最方便快捷的。
```python
pip install APScheduler
# 如果出现因下载失败导致安装不上的情况，建议使用代理
pip --proxy http://代理ip:端口 install APScheduler
```

## 3.使用步骤
APScheduler 使用起来还算是比较简单。运行一个调度任务只需要以下三部曲。
1) 新建一个 schedulers (调度器) 。
2) 添加一个调度任务(job stores)。
3) 运行调度任务。

下面是执行每 2 秒报时的简单示例代码：
```python
import datetime
import time
from apscheduler.schedulers.background import BackgroundScheduler

def timedTask():
    print(datetime.datetime.utcnow().strftime("%Y-%m-%d %H:%M:%S.%f")[:-3])

if __name__ == '__main__':
    # 创建后台执行的 schedulers
    scheduler = BackgroundScheduler()  
    # 添加调度任务
    # 调度方法为 timedTask，触发器选择 interval(间隔性)，间隔时长为 2 秒
    scheduler.add_job(timedTask, 'interval', seconds=2)
    # 启动调度任务
    scheduler.start()
    
    while True:
        print(time.time())
        time.sleep(5)
```

## 4.基础组件
APScheduler 有四种组件，分别是：`调度器(scheduler)`，`作业存储(job store)`，`触发器(trigger)`，`执行器(executor)`。

- schedulers（调度器）
它是任务调度器，属于控制器角色。它配置作业存储器和执行器可以在调度器中完成，例如添加、修改和移除作业。

- triggers（触发器）
描述调度任务被触发的条件。不过触发器完全是无状态的。

- job stores（作业存储器）
任务持久化仓库，默认保存任务在内存中，也可将任务保存都各种数据库中，任务中的数据序列化后保存到持久化数据库，从数据库加载后又反序列化。

- executors（执行器）
负责处理作业的运行，它们通常通过在作业中提交指定的可调用对象到一个线程或者进城池来进行。当作业完成时，执行器将会通知调度器。

### 4-1.schedulers（调度器）
我个人觉得 APScheduler 非常好用的原因。它提供 7 种调度器，能够满足我们各种场景的需要。

例如：后台执行某个操作，异步执行操作等。调度器分别是：
- BlockingScheduler : 调度器在当前进程的主线程中运行，也就是会阻塞当前线程。
- BackgroundScheduler : 调度器在后台线程中运行，不会阻塞当前线程。
- AsyncIOScheduler : 结合 `asyncio` 模块（一个异步框架）一起使用。
- GeventScheduler : 程序中使用 `gevent`（高性能的Python并发框架）作为IO模型，和 `GeventExecutor` 配合使用。
- TornadoScheduler : 程序中使用 `Tornado`（一个web框架）的IO模型，用 `ioloop.add_timeout` 完成定时唤醒。
- TwistedScheduler : 配合 `TwistedExecutor`，用 `reactor.callLater` 完成定时唤醒。
- QtScheduler : 你的应用是一个 Qt 应用，需使用QTimer完成定时唤醒。

### 4-2.triggers（触发器）
APScheduler 有三种内建的 trigger:

#### 1）date 触发器
date 是最基本的一种调度，作业任务只会执行一次。它表示特定的时间点触发。它的参数如下：

| 参数 |  说明  | 
| :--------: | :--------| 
| run_date (datetime 或 str)  |  作业的运行日期或时间 | 
| timezone (datetime.tzinfo 或 str) | 指定时区  |

date 触发器使用示例如下：

```python
from datetime import datetime
from datetime import date
from apscheduler.schedulers.background import BackgroundScheduler

def job_func(text):
    print(text)

scheduler = BackgroundScheduler()
# 在 2017-12-13 时刻运行一次 job_func 方法
scheduler .add_job(job_func, 'date', run_date=date(2017, 12, 13), args=['text'])
# 在 2017-12-13 14:00:00 时刻运行一次 job_func 方法
scheduler .add_job(job_func, 'date', run_date=datetime(2017, 12, 13, 14, 0, 0), args=['text'])
# 在 2017-12-13 14:00:01 时刻运行一次 job_func 方法
scheduler .add_job(job_func, 'date', run_date='2017-12-13 14:00:01', args=['text'])

scheduler.start()
```

#### 2）interval 触发器
固定时间间隔触发。interval 间隔调度，参数如下：

| 参数 |  说明  | 
| :--------: | :--------| 
| weeks (int)  |  间隔几周 | 
| days (int) | 间隔几天  |  
| hours (int) | 间隔几小时  |  
| minutes (int) | 间隔几分钟 |  
| seconds (int) | 间隔多少秒 |  
| start_date (datetime 或 str) | 开始日期 |  
| end_date (datetime 或 str) | 结束日期 |
| timezone (datetime.tzinfo 或str)  | 时区 |

interval 触发器使用示例如下：

```python
import datetime
from apscheduler.schedulers.background import BackgroundScheduler

def job_func(text):
    print(datetime.datetime.utcnow().strftime("%Y-%m-%d %H:%M:%S.%f")[:-3])

scheduler = BackgroundScheduler()
# 每隔两分钟执行一次 job_func 方法
scheduler .add_job(job_func, 'interval', minutes=2)
# 在 2017-12-13 14:00:01 ~ 2017-12-13 14:00:10 之间, 每隔两分钟执行一次 job_func 方法
scheduler .add_job(job_func, 'interval', minutes=2, start_date='2017-12-13 14:00:01' , end_date='2017-12-13 14:00:10')

scheduler.start()
```

#### 3）cron 触发器
 在特定时间周期性地触发，和Linux crontab格式兼容。它是功能最强大的触发器。
 我们先了解 cron 参数：

| 参数 |  说明  | 
| :--------: | :--------| 
| year (int 或 str)   |  年，4位数字 | 
| month (int 或 str)  | 月 (范围1-12) |
| day (int 或 str)   |  日 (范围1-31| 
| week (int 或 str)   |  周 (范围1-53)| 
| day_of_week (int 或 str)   |  周内第几天或者星期几 (范围0-6 或者 mon,tue,wed,thu,fri,sat,sun) | 
| hour (int 或 str)   |   时 (范围0-23) | 
| minute (int 或 str)   |  分 (范围0-59) | 
| second (int 或 str)   |  秒 (范围0-59) | 
| start_date (datetime 或 str) | 最早开始日期(包含) |
| end_date (datetime 或 str) | 最晚结束时间(包含) |
| timezone (datetime.tzinfo 或str)  | 指定时区 |

这些参数是支持算数表达式，取值格式有如下：
![取值格式](https://img.jikehou.cn/img/20171208_4.png)

cron 触发器使用示例如下：
```python
import datetime
from apscheduler.schedulers.background import BackgroundScheduler

def job_func(text):
    print("当前时间：", datetime.datetime.utcnow().strftime("%Y-%m-%d %H:%M:%S.%f")[:-3])

scheduler = BackgroundScheduler()
# 在每年 1-3、7-9 月份中的每个星期一、二中的 00:00, 01:00, 02:00 和 03:00 执行 job_func 任务
scheduler .add_job(job_func, 'cron', month='1-3,7-9',day='0, tue', hour='0-3')

scheduler.start()
```

### 4-3.作业存储(job store)
该组件是对调度任务的管理。

#### 1）添加 job
有两种添加方法，其中一种上述代码用到的 `add_job()`， 另一种则是`scheduled_job()`修饰器来修饰函数。

这个两种办法的区别是：第一种方法返回一个 apscheduler.job.Job 的实例，可以用来改变或者移除 job。第二种方法只适用于应用运行期间不会改变的 job。

第二种添加任务方式的例子：
```python
import datetime
from apscheduler.schedulers.background import BackgroundScheduler

@scheduler.scheduled_job(job_func, 'interval', minutes=2)
def job_func(text):
    print(datetime.datetime.utcnow().strftime("%Y-%m-%d %H:%M:%S.%f")[:-3])

scheduler = BackgroundScheduler()
scheduler.start()
```

#### 2）移除 job
移除 job 也有两种方法：`remove_job()` 和 `job.remove()`。
remove_job() 是根据 job 的 id 来移除，所以要在 job 创建的时候指定一个 id。
job.remove() 则是对 job 执行 remove 方法即可

```python
scheduler.add_job(job_func, 'interval', minutes=2, id='job_one')
scheduler.remove_job(job_one)

job = add_job(job_func, 'interval', minutes=2, id='job_one')
job.remvoe()
```

#### 3）获取 job 列表
通过 `scheduler.get_jobs()` 方法能够获取当前调度器中的所有 job 的列表

#### 4）修改 job
如果你因计划改变要对 job 进行修改，可以使用`Job.modify()` 或者 `modify_job()`方法来修改 job 的属性。但是值得注意的是，job 的 id 是无法被修改的。

```python
scheduler.add_job(job_func, 'interval', minutes=2, id='job_one')
scheduler.start()
# 将触发时间间隔修改成 5分钟
scheduler.modify_job('job_one', minutes=5)

job = scheduler.add_job(job_func, 'interval', minutes=2)
# 将触发时间间隔修改成 5分钟
job.modify(minutes=5)
```

#### 5）关闭 job
默认情况下调度器会等待所有正在运行的作业完成后，关闭所有的调度器和作业存储。如果你不想等待，可以将 wait 选项设置为 False。
```python
scheduler.shutdown()
scheduler.shutdown(wait=false)
```

### 4-4.执行器(executor)
执行器顾名思义是执行调度任务的模块。最常用的 executor 有两种：`ProcessPoolExecutor` 和 `ThreadPoolExecutor`

下面是显式设置 job store(使用mongo存储)和 executor 的代码的示例。
注：本代码来源于网络

```python
from pymongo import MongoClient
from apscheduler.schedulers.blocking import BlockingScheduler
from apscheduler.jobstores.mongodb import MongoDBJobStore
from apscheduler.jobstores.memory import MemoryJobStore
from apscheduler.executors.pool import ThreadPoolExecutor, ProcessPoolExecutor
 
 
def my_job():
    print 'hello world'
host = '127.0.0.1'
port = 27017
client = MongoClient(host, port)
 
jobstores = {
    'mongo': MongoDBJobStore(collection='job', database='test', client=client),
    'default': MemoryJobStore()
}
executors = {
    'default': ThreadPoolExecutor(10),
    'processpool': ProcessPoolExecutor(3)
}
job_defaults = {
    'coalesce': False,
    'max_instances': 3
}
scheduler = BlockingScheduler(jobstores=jobstores, executors=executors, job_defaults=job_defaults)
scheduler.add_job(my_job, 'interval', seconds=5)
 
try:
    scheduler.start()
except SystemExit:
    client.close() 
```
