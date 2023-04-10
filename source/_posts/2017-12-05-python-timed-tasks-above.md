---
layout: post
title:  "Python 定时任务(上)"
date:   2017-12-05 21:27:59
urlname: 26
cover: https://img.jikehou.cn/cover/2017-12-05.jpg
categories: [小白入门Python]
tags: [Python, 定时任务]
keywords: [Python, datetime, time, sched模块]
---
> 在项目中，我们可能遇到有定时任务的需求。其一：定时执行任务。例如每天早上 8 点定时推送早报。其二：每隔一个时间段就执行任务。比如：每隔一个小时提醒自己起来走动走动，避免长时间坐着。

今天，我跟大家分享下 Python 定时任务的实现方法。
<!-- more -->
## 1.
第一种办法是最简单又最暴力。那就是在一个死循环中，使用线程睡眠函数 sleep()。

```python
from datetime import datetime
import time

'''
每个 10 秒打印当前时间。
'''
def timedTask():
    while True:
        print(datetime.now().strftime("%Y-%m-%d %H:%M:%S"))
        time.sleep(10)

if __name__ == '__main__':
    timedTask()
```
这种方法能够执行固定间隔时间的任务。如果`timedTask()`函数之后还有些操作，我们还使用死循环 + 阻塞线程。

这会使得`timedTask()`一直占有 CPU 资源，导致后续操作无法执行。

我建议谨重使用。

## 2.
既然第一种方法暴力，那么有没有比较优雅地方法？答案是肯定的。

Python 标准库 threading 中有个 Timer 类。

它会新启动一个线程来执行定时任务，所以它是非阻塞函式。

如果你有使用多线程的话，需要关心线程安全问题。那么你可以选使用`threading.Timer`模块。
```python
from datetime import datetime
from threading import Timer
import time

'''
每个 10 秒打印当前时间。
'''
def timedTask():
    '''
    第一个参数: 延迟多长时间执行任务(单位: 秒)
    第二个参数: 要执行的任务, 即函数
    第三个参数: 调用函数的参数(tuple)
    '''
    Timer(10, task, ()).start()

# 定时任务
def task():
    print(datetime.now().strftime("%Y-%m-%d %H:%M:%S"))

if __name__ == '__main__':
    timedTask()
    while True:
        print(time.time())
        time.sleep(5)
```

运行结果：
```python
1512486945.1196375
1512486950.119873
2017-12-05 23:15:50
1512486955.133385
```

## 3.
第三种方式是也是使用标准库中`sched`模块。sched 是事件调度器，它通过 `scheduler` 类来调度事件，从而达到定时执行任务的效果。

`sched`库使用起来也是非常简单。

### 1）首先构造一个`sched.scheduler`类
它接受两个参数：`timefunc` 和 `delayfunc`。

timefunc 应该返回一个数字，代表当前时间，delayfunc 函数接受一个参数，用于暂停运行的时间单元。

一般使用默认参数就行，即传入这两个参数 `time.time` 和 `time.sleep`.

当然，你也可以自己实现时间暂停的函数。

### 2）添加调度任务
`scheduler` 提供了两个添加调度任务的函数:

- `enter(delay, priority, action, argument=(), kwargs={})`

该函数可以延迟一定时间执行任务。`delay` 表示延迟多长时间执行任务，单位是秒。`priority`为优先级，越小优先级越大。

两个任务指定相同的延迟时间，优先级大的任务会向被执行。

`action` 即需要执行的函数，`argument` 和 `kwargs` 分别是函数的位置和关键字参数。

- `scheduler.enterabs(time, priority, action, argument=(), kwargs={})`

添加一项任务，但这个任务会在 `time` 这时刻执行。因此，`time` 是绝对时间.其他参数用法与 `enter()` 中的参数用法是一致。

### 3）把任务运行起来
调用 `scheduler.run()`函数就完事了。

下面是 sche 使用的简单示例：
```python
from datetime import datetime
import sched
import time

'''
每个 10 秒打印当前时间。
'''
def timedTask():
    # 初始化 sched 模块的 scheduler 类
    scheduler = sched.scheduler(time.time, time.sleep)
    # 增加调度任务
    scheduler.enter(10, 1, task)
    # 运行任务
    scheduler.run()

# 定时任务
def task():
    print(datetime.now().strftime("%Y-%m-%d %H:%M:%S"))

if __name__ == '__main__':
    timedTask()
```

值得注意的是： scheduler 中的每个调度任务只会工作一次，不会无限循环被调用。如果想重复执行同一任务， 需要重复添加调度任务即可。