---
layout: post
title:  "用 Python 学习数据结构, 有它就不用愁"
date:   2017-08-29 09:36:17 +0800
urlname: 15
cover: https://cdn.jsdelivr.net/gh/monkey-soft/img@master/cover/2017-08-29.jpg
categories: [小白入门Python]
tags: [Python, 数据结构]
keywords: [Python, 数据结构, Pygorithm]

---
数据结构，我们对它已经是耳熟能详。

对于计算机相关专业的大学生来说，它是一门专业必修课。

从事软件开发的人员则把它作为谋生必备技能。这充分体现数据结构的重要性。

因此，我们对数据结构是不得不学。

虽然数据结构的实现不限制语言，但市面上很多教程书籍都是以 C 语言作为编程语言进行讲解。

如果你喜欢且在学习 Python，可能会陷入苦于这样的烦恼中。
<!-- more -->
那就是没有 Python 版本的数据结构实现代码。

莫慌！我给大家推荐一个第三方库，它能让你这种烦恼立刻云消雾散。


{% btn 'https://github.com/OmkarPathak/pygorithm', GitHub仓库, far fa-hand-point-right,blue larger %}


Pygorithm 是由一个热心肠的印度小哥编写的开源项目。

他编写创建该库的初衷是处于教学目的。

我们不仅可以阅读源码的方式学习数据结构，而且可以把它当做现成工具来使用。

## 1.安装
安装 python 库，我推荐使用 pip 方式，方便又省事。

```python
pip install Pygorithm
# 如果出现因下载失败导致安装不上的情况，可以先启动 ss 再执行安装命令
# 或者在终端中使用代理
pip --proxy http://代理ip:端口 install Pygorithm
```

## 2.支持的类型
Pygorithm 实现的数据结构类型有以下这几种，括号中表示包名。

### 2-1.栈
- 栈 (data_structures.stack.Stack) 
- 中缀表达式转换为后缀表达式 (data_structures.stack.InfixToPostfix)

### 2-2.队列
- 队列 (data_structures.queue.Queue)
- 双端队列 (data_structures.queue.Deque)

### 2-3.链表
- 单向链表 (data_structures.linked_list.SinglyLinkedList)
- 双向链表 (data_structures.linked_list.DoublyLinkedList)

### 2-4.树
- 二叉树  (data_structures.tree.BinaryTree)
- 搜索二叉树 (data_structures.tree.BinarySearchTree)

### 2-5.图
- 图 (data_structures.graph.Graph)
- 拓扑排序 (data_structures.graph.TopologicalSort)
- 有向图 (data_structures.graph.CheckCycleDirectedGraph)
- 无向图 (data_structures.graph.CheckCycleUndirectedGraph)

### 2-6.堆
- 堆 (data_structures.heap.Heap)

### 2-7.字典树
- 字典树 （data_structures.trie.Trie）


## 3.常见算法
你也许没有想到吧。Pygorithm 中也实现一些常见的路径搜索、查找、排序等算法。

### 3-1.常见的路径搜索算法：
- Dijkstra(迪杰斯特拉)
- Unidirectional AStar（单向 A*算法）
- BiDirectional AStar（双向 A*算法）

### 3-2.常见的查找算法：
- Linear Search (线性查找)
- Binary Search (二分法查找)
- Breadth First Search (广度优先搜索)
- Depth First Search (深度优先搜索)

### 3-3.常见的排序算法：
- bubble_sort（冒泡算法）
- bucket_sort（桶排序）
- counting_sort （计数排序）
- heap_sort（堆排序）
- insertion_sort（插入排序）
- merge_sort（归并排序）
- quick_sort （快速排序）
- selection_sort（选择排序）
- shell_sort（希尔排序）