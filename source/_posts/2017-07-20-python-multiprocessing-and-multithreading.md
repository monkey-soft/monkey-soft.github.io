---
layout: post
title:  "Python 多进程与多线程"
date:   2017-07-20 16:28:12
urlname: 10
cover: https://img.jikehou.cn/cover/2017-07-20.jpg
categories: [Python爬虫入门]
tags: [Python, 网络爬虫, 多线程, 多进程]
keywords: [Python, 网络爬虫, 多线程, GIL, 多进程]
---

> 前言：为什么有人说 Python 的多线程是鸡肋，不是真正意义上的多线程？

看到这里，也许你会疑惑。这很正常，所以让我们带着问题来阅读本文章吧。

问题：
1、Python 多线程为什么耗时更长？
2、为什么在 Python 里面推荐使用多进程而不是多线程？
<!-- more -->
## 1.基础知识
现在的 PC 都是多核的，使用多线程能充分利用 CPU 来提供程序的执行效率。

### 1-1.线程
线程是一个基本的 CPU 执行单元，它必须依托于进程存活。

一个线程是一个**execution context（执行上下文）**，即一个 CPU 执行时所需要的一串指令。

### 1-2.进程
进程是指一个程序在给定数据集合上的一次执行过程，是系统进行资源分配和运行调用的独立单位。

可以简单地理解为操作系统中正在执行的程序。也就说，每个应用程序都有一个自己的进程。

每一个进程启动时都会最先产生一个线程，即主线程。然后主线程会再创建其他的子线程。

### 1-3.两者的区别

- 线程必须在某个进行中执行。
- 一个进程可包含多个线程，其中有且只有一个主线程。
- 多线程共享同个地址空间、打开的文件以及其他资源。
- 多进程共享物理内存、磁盘、打印机以及其他资源。

### 1-4.线程的类型

线程的因作用可以划分为不同的类型。大致可分为：
- 主线程
- 子线程
- 守护线程（后台线程）
- 前台线程

## 2.Python 多线程
### 2-1.GIL
其他语言，CPU 是多核时是支持多个线程同时执行。但在 Python 中，无论是单核还是多核，同时只能由一个线程在执行。其根源是 GIL 的存在。

GIL 的全称是 Global Interpreter Lock(全局解释器锁)，来源是 Python 设计之初的考虑，为了数据安全所做的决定。

某个线程想要执行，必须先拿到 GIL，我们可以把 GIL 看作是“通行证”，并且在一个 Python 进程中，GIL 只有一个。拿不到通行证的线程，就不允许进入 CPU 执行。

而目前 Python 的解释器有多种，例如：

- **CPython**：CPython 是用C语言实现的 Python 解释器。 作为官方实现，它是最广泛使用的 Python 解释器。

- **PyPy**：PyPy 是用RPython实现的解释器。RPython 是 Python 的子集， 具有静态类型。这个解释器的特点是即时编译，支持多重后端（C, CLI, JVM）。PyPy 旨在提高性能，同时保持最大兼容性（参考 CPython 的实现）。

- **Jython**：Jython 是一个将 Python 代码编译成 Java 字节码的实现，运行在JVM (Java Virtual Machine) 上。另外，它可以像是用 Python 模块一样，导入 并使用任何Java类。

- **IronPython**：IronPython 是一个针对 .NET 框架的 Python 实现。它 可以用 Python 和 .NET framewor k的库，也能将 Python 代码暴露给 .NET 框架中的其他语言。

GIL 只在 CPython 中才有，而在 PyPy 和 Jython 中是没有 GIL 的。

每次释放 GIL锁，线程进行锁竞争、切换线程，会消耗资源。这就导致打印线程执行时长，会发现耗时更长的原因。

并且由于 GIL 锁存在，Python 里一个进程永远只能同时执行一个线程(拿到 GIL 的线程才能执行)，这就是为什么在多核CPU上，Python 的多线程效率并不高的根本原因。

### 2-2.创建多线程
Python提供两个模块进行多线程的操作，分别是`thread`和`threading`，
前者是比较低级的模块，用于更底层的操作，一般应用级别的开发不常用。

- 方法1：直接使用`threading.Thread()`

```python
import threading

# 这个函数名可随便定义
def run(n):
    print("current task：", n)

if __name__ == "__main__":
    t1 = threading.Thread(target=run, args=("thread 1",))
    t2 = threading.Thread(target=run, args=("thread 2",))
    t1.start()
    t2.start()
```

- 方法2：继承`threading.Thread`来自定义线程类，重写`run`方法

```python
import threading

class MyThread(threading.Thread):
    def __init__(self, n):
        super(MyThread, self).__init__()  # 重构run函数必须要写
        self.n = n

    def run(self):
        print("current task：", n)

if __name__ == "__main__":
    t1 = MyThread("thread 1")
    t2 = MyThread("thread 2")

    t1.start()
    t2.start()
```

### 2-3.线程合并
`Join`函数执行顺序是逐个执行每个线程，执行完毕后继续往下执行。主线程结束后，子线程还在运行，`join`函数使得主线程等到子线程结束时才退出。

```python
import threading

def count(n):
    while n > 0:
        n -= 1

if __name__ == "__main__":
    t1 = threading.Thread(target=count, args=("100000",))
    t2 = threading.Thread(target=count, args=("100000",))
    t1.start()
    t2.start()
    # 将 t1 和 t2 加入到主线程中
    t1.join()
    t2.join()
```

### 2-4.线程同步与互斥锁
线程之间数据共享的。当多个线程对某一个共享数据进行操作时，就需要考虑到线程安全问题。`threading`模块中定义了Lock 类，提供了互斥锁的功能来保证多线程情况下数据的正确性。

用法的基本步骤：
```python
#创建锁
mutex = threading.Lock()
#锁定
mutex.acquire([timeout])
#释放
mutex.release()
```
其中，锁定方法acquire可以有一个超时时间的可选参数timeout。如果设定了timeout，则在超时后通过返回值可以判断是否得到了锁，从而可以进行一些其他的处理。

具体用法见示例代码：
```python
import threading
import time

num = 0
mutex = threading.Lock()

class MyThread(threading.Thread):
    def run(self):
        global num 
        time.sleep(1)

        if mutex.acquire(1):  
            num = num + 1
            msg = self.name + ': num value is ' + str(num)
            print(msg)
            mutex.release()

if __name__ == '__main__':
    for i in range(5):
        t = MyThread()
        t.start()
```

### 2-5.可重入锁（递归锁）
为了满足在同一线程中多次请求同一资源的需求，Python 提供了可重入锁（RLock）。

`RLock`内部维护着一个`Lock`和一个`counter`变量，counter 记录了 acquire 的次数，从而使得资源可以被多次 require。直到一个线程所有的 acquire 都被 release，其他的线程才能获得资源。

具体用法如下：
```python
#创建 RLock
mutex = threading.RLock()

class MyThread(threading.Thread):
    def run(self):
        if mutex.acquire(1):
            print("thread " + self.name + " get mutex")
            time.sleep(1)
            mutex.acquire()
            mutex.release()
            mutex.release()
```

### 2-6.守护线程
如果希望主线程执行完毕之后，不管子线程是否执行完毕都随着主线程一起结束。

我们可以使用`setDaemon(bool)`函数，它跟`join`函数是相反的。它的作用是设置子线程是否随主线程一起结束，必须在`start()` 之前调用，默认为`False`。

### 2-7.定时器
如果需要规定函数在多少秒后执行某个操作，需要用到`Timer`类。具体用法如下：

```python
from threading import Timer
 
def show():
    print("Pyhton")

# 指定一秒钟之后执行 show 函数
t = Timer(1, hello)
t.start()  
```

## 3.Python 多进程

### 3-1.创建多进程 
Python 要进行多进程操作，需要用到`muiltprocessing`库，其中的`Process`类跟`threading`模块的`Thread`类很相似。所以直接看代码熟悉多进程。

- 方法1：直接使用`Process`, 代码如下：

```python
from multiprocessing import Process  

def show(name):
    print("Process name is " + name)

if __name__ == "__main__": 
    proc = Process(target=show, args=('subprocess',))  
    proc.start()  
    proc.join()
```

- 方法2：继承`Process`来自定义进程类，重写`run`方法, 代码如下：

```python
from multiprocessing import Process
import time

class MyProcess(Process):
    def __init__(self, name):
        super(MyProcess, self).__init__()
        self.name = name

    def run(self):
        print('process name :' + str(self.name))
        time.sleep(1)

if __name__ == '__main__':
    for i in range(3):
        p = MyProcess(i)
        p.start()
    for i in range(3):
        p.join()
```

### 3-2.多进程通信
进程之间不共享数据的。如果进程之间需要进行通信，则要用到`Queue模块`或者`Pipi模块`来实现。

- **Queue**

Queue 是多进程安全的队列，可以实现多进程之间的数据传递。它主要有两个函数,`put`和`get`。

put() 用以插入数据到队列中，put 还有两个可选参数：blocked 和 timeout。如果 blocked 为 True（默认值），并且 timeout 为正值，该方法会阻塞 timeout 指定的时间，直到该队列有剩余的空间。

如果超时，会抛出 Queue.Full 异常。如果 blocked 为 False，但该 Queue 已满，会立即抛出 Queue.Full 异常。

get()可以从队列读取并且删除一个元素。同样，get 有两个可选参数：blocked 和 timeout。如果 blocked 为 True（默认值），并且 timeout 为正值，那么在等待时间内没有取到任何元素，会抛出 Queue.Empty 异常。

如果blocked 为 False，有两种情况存在，如果 Queue 有一个值可用，则立即返回该值，否则，如果队列为空，则立即抛出 Queue.Empty 异常。

具体用法如下：
```python
from multiprocessing import Process, Queue
 
def put(queue):
    queue.put('Queue 用法')
 
if __name__ == '__main__':
    queue = Queue()
    pro = Process(target=put, args=(queue,))
    pro.start()
    print(queue.get())   
    pro.join()
```

- **Pipe**

Pipe的本质是进程之间的用管道数据传递，而不是数据共享，这和socket有点像。

pipe() 返回两个连接对象分别表示管道的两端，每端都有send() 和recv()函数。

如果两个进程试图在同一时间的同一端进行读取和写入那么，这可能会损坏管道中的数据。

具体用法如下：
```python
from multiprocessing import Process, Pipe
 
def show(conn):
    conn.send('Pipe 用法')
    conn.close()
 
if __name__ == '__main__':
    parent_conn, child_conn = Pipe() 
    pro = Process(target=show, args=(child_conn,))
    pro.start()
    print(parent_conn.recv())   
    pro.join()
```

### 3-3.进程池
创建多个进程，我们不用傻傻地一个个去创建。我们可以使用`Pool`模块来搞定。

Pool 常用的方法如下：

| 方法      |    含义 | 
| :-------- |:--------:| 
| apply()  | 同步执行（串行） | 
| apply_async()  | 异步执行（并行） | 
| terminate()  | 立刻关闭进程池 | 
| join()  | 主进程等待所有子进程执行完毕。必须在close或terminate()之后使用 |
| close()  | 等待所有进程结束后，才关闭进程池 | 

具体用法见示例代码：
```python
from multiprocessing import Pool
def show(num):
    print('num : ' + str(num))

if __name__=="__main__":
    pool = Pool(processes = 3)
    for i in xrange(6):
        # 维持执行的进程总数为processes，当一个进程执行完毕后会添加新的进程进去
        pool.apply_async(show, args=(i, ))       
    print('======  apply_async  ======')
    pool.close()
    #调用join之前，先调用close函数，否则会出错。执行完close后不会有新的进程加入到pool,join函数等待所有子进程结束
    pool.join()
```


## 4.选择多线程还是多进程？
在这个问题上，首先要看下你的程序是属于哪种类型的。一般分为两种 CPU 密集型 和 I/O 密集型。

- CPU 密集型：程序比较偏重于计算，需要经常使用 CPU 来运算。例如科学计算的程序，机器学习的程序等。

- I/O 密集型：顾名思义就是程序需要频繁进行输入输出操作。爬虫程序就是典型的 I/O 密集型程序。

如果程序是属于 CPU 密集型，建议使用多进程。而多线程就更适合应用于 I/O 密集型程序。