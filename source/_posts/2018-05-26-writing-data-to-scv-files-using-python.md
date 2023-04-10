---
layout: post
title:  "使用 Python 将数据写到 CSV 文件"
date:   2018-05-26 01:19:49
urlname: 56
cover: https://img.jikehou.cn/cover/2018-05-26.jpg
categories: [小白入门Python]
tags: [Python, pandas]
keywords: [Python, Unicode , csv, codecs, pandas]
---
我们从网上爬取数据，最后一步会考虑如何存储数据。

如果数据量不大，往往不会选择存储到数据库，而是选择存储到文件中，例如文本文件、CSV 文件、xls 文件等。

因为文件具备携带方便、查阅直观。

Python 作为胶水语言，搞定这些当然不在话下。

但在写数据过程中，经常因数据源中带有中文汉字而报错。最让人头皮发麻的编码问题。

我先说下编码相关的知识。

编码方式有很多种：UTF-8, GBK, ASCII 等。
<!-- more -->
ASCII 码是美国在上个世纪 60 年代制定的一套字符编码。

主要是规范英语字符和二进制位之间的关系。

英语词汇组成简单，由 26 个字母构成。`使用一个字节就能表示一个字母符号。外加各种符号，使用 128 个字符就满足编码要求`。

不同国家有不同语言文字。

同时，文字组成部分的数量相比英语字母要多很多。

根据不完全统计，汉字的数量大约将近 10 万个，日常所使用的汉字有 3000 个。

显然，ASCII 编码无法满足需求。`所以汉字采用 GBK 编码，使用两个字节表示一个汉字`。

简体中文的编码方式是 GBK2312。

那 UTF-8 又是什么编码？

这要先说 Unicode 了。

Unicode 目的是为了统一各种编码。因为各国都各自的编码方式。

如果使用一种编码编码，使用另一种编码解码。这会造成出现乱码的情况。

但 Unicode 只是一个符号集，它只规定了符号的二进制代码，却没有规定这个二进制代码应该如何存储。

**UTF-8 就是在互联网上使用最广的一种 Unicode 的实现方式**。

因此，`如果我们要写数据到文件中，最好指定编码形式为 UTF-8`。

Python 标准库中，有个名为 **csv** 的库，专门处理 csv 的读写操作。具体使用实例如下：

```python
import csv
import codecs
# codecs 是自然语言编码转换模块

fileName = 'PythonBook.csv'

# 指定编码为 utf-8, 避免写 csv 文件出现中文乱码
with codecs.open(fileName, 'w', 'utf-8') as csvfile:
    # 指定 csv 文件的头部显示项
    filednames = ['书名', '作者']
    writer = csv.DictWriter(csvfile, fieldnames=filednames)

    books = []
    book = {
        'title': '笑傲江湖',
        'author': '金庸',
    }
    books.append(book)

    writer.writeheader()
    for book in books:
        try:
            writer.writerow({'书名':book['title'], '作者':book['author']})
        except UnicodeEncodeError:
            print("编码错误, 该数据无法写到文件中, 直接忽略该数据")
```

这种方式是逐行往 CSV 文件中写数据， 所以效率会比较低。

如果想批量将数据写到 CSV 文件中，需要用到 `pandas` 库。

pandas 是第三方库，所以使用之前需要安装。通过 pip 方式安装是最简单、最方便的。

```cmd
pip install pandas
```

使用 pandas 批量写数据的用法如下：

```python
import pandas as pd

fileName = 'PythonBook.csv'
number = 1

books = []
book = {
    'title': '笑傲江湖',
    'author': '金庸',
}
# 如果 book 条数足够多的话，pandas 会每次往文件中写 50 条数据。
books.append(book)

data = pd.DataFrame(books)
# 写入csv文件,'a+'是追加模式
try:
    if number == 1:
        csv_headers = ['书名', '作者']
        data.to_csv(fileName, header=csv_headers, index=False, mode='a+', encoding='utf-8')
    else:
        data.to_csv('fileName, header=False, index=False, mode='a+', encoding='utf-8')
        number = number + 1
except UnicodeEncodeError:
    print("编码错误, 该数据无法写到文件中, 直接忽略该数据")
```
