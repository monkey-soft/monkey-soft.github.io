---
layout: post
title:  "Django 学习笔记之模型高级用法(上)"
date:   2018-04-24 21:14:07
urlname: 49
cover: https://img.jikehou.cn/cover/2018-04-24.jpg
categories: [Python编程]
tags: [Python, Web开发]
keywords: [Python, web, Django]
---
前面有两篇文章简单介绍 Django 的模型，这一部分算是基础知识。

我自己近期也总做了下总结，将花大概两篇的篇幅来分享下模型的一些高级用法。

如果想熟悉 Django 的用法，我认为应该一开始要熟悉一些细节用法，后面再了解 Django 的实现原理。

而细节用法往往体现在一些差别用法，难以理解的知识点上。
<!-- more -->
## 1.复杂的字段类型

经过前面的学习，我们知道模型的字段类型一方面是指定数据库表的列名称和数据类型，另一方面决定 HTML 中的表单标签类型。

### 1-1.整数类型的区别
Django 的整数类型有三个，分别是 **IntegerField**、**BigIntegerField** 和 **SmallIntegerField**。

这三个字段区别在于取值范围。

IntegerField 在 Django 所有支持的数据库中，合法取值范围是 -2147483648 到 2147483647。

而 BigIntegerField 是一个 64 位整数，它允许的值范围是 -9223372036854775808 到 9223372036854775807。

所以在数据库迁移的时候，特别数据库中有 Sqlite 时，要更加注意数字的取值范围。

SmallIntegerField 取值范围是 -32768 到 32767。

### 1-2.自增类型的区别
**AutoFiled** 和 **BigAutoFiled** 都是自增类型，它们都是由整数类型演化而来。

AutoFiled 是一个根据实际 ID 自动增长的 IntegerField。

通常不需要直接使用它,如果表中没有设置主键时，Django 将会自动添加一个自增主键。

BigAutoField 其实也是一个 BigIntegerField，但它支持 ID 自动增长。

所以它的取值范围不能为负数和零了。

### 1-3.时间类型
DateField 和 DateTimeField 中的两个重要属性 **auto_now** 和 **auto_now_add** 默认值都是 Flase。 

设置 auto_now 或者 auto_now_add 的值为 True，间接给该字段设置了 editable=False 和 blank=True 。

给参数赋值需要传递一个 datetime.date 对象。

如果时间是一串字符串，则转化为 date 对象。

DateField 支持输入值的形式如下：
```python
['%Y-%m-%d',      # '2006-10-25'
 '%m/%d/%Y',      # '10/25/2006'
 '%m/%d/%y']      # '10/25/06'
```

DateTimeField 支持输入值的形式如下：
```python
['%b %d %Y',      # 'Oct 25 2006'
 '%b %d, %Y',     # 'Oct 25, 2006'
 '%d %b %Y',      # '25 Oct 2006'
 '%d %b, %Y',     # '25 Oct, 2006'
 '%B %d %Y',      # 'October 25 2006'
 '%B %d, %Y',     # 'October 25, 2006'
 '%d %B %Y',      # '25 October 2006'
 '%d %B, %Y']     # '25 October, 2006'
```

### 1-4.FilePathField
该字段是用于保存文件路径信息的。默认最大长度为 100，当可通过 max_length 参数自定义。它包含几个重要的参数：

**path**：必传参数。记录目录或者文件的绝对路径。例如：`/home/monkey`
**match**：可选参数，它是一个正则表达式，主要用于匹配过滤出文件名。
**recursive**：可选参数，表示是否包含子目录。默认值为 Flase。
**allow_files**：可选参数，表示是否将文件名包括在内，默认值为 True。
**allow_folders**：可选参数，表示是否将目录名包括在内默认值为 Flase。

Django 规定 allow_files 和 allow_folders 两者之间必须有一个值为 True。

### 1-5.FileField
上传文件字段，常见于表单中。一般而言，文件都是保存在服务器的硬盘中。

因此，该字段在数据库中其实是一个字符串类型，默认最大长度100，可以通过max_length参数自定义。

FileField 有两个重要的可选参数：**upload_to** 和 **storage**

- upload_to
upload_to 是指定文件上传的目录。用法如下：
```python
class MyModel(models.Model):
    # 文件上传到 MEDIA_ROOT/uploads
    upload = models.FileField(upload_to='uploads/')
    # 或者
    # 文件上传到 MEDIA_ROOT/uploads/2015/01/30
    upload = models.FileField(upload_to='uploads/%Y/%m/%d/')
```
其中 **MEDIA_ROOT** 是在 settings.py 中设置，表示上传文件的根目录。
另外还需要设置 **MEDIA_URL**, 它表示上传文件对外能访问的 url 地址。

- Storage 
Storage 是一个文件操作对象。它提供 size(path)、open(path).read()、delete(path)、exists(path)等方法来操作文件。

### 1-6.ImageField
保存图像文件的字段。ImageField 用法跟 FileField 类似。除了需要在 seeting.py 中增加相关配置，还都拥有共同的 upload_to 字段选项。

它还有额外的可选参数：一个是 **height_field**，表示保存图片的高度。 另一个是 **width_field**，表示保存图片的宽度。

## 2.关系字段
之前文章讲了三种关系字段的类型、定义、作用。今天讲下其中的一些字段选项。

### 2-1.ForeignKey

**1）on_delete**

在 Django 2.0 中，设置外键时需要添加一个 **on_delete** 选项。

外键本身涉及到两个表的数据，况且外键在数据库中是有约束行为。

所以 on_delete 参数是 Django 模拟 SQL 约束的行为。

on_delete 有几个可选值：
* `CASCADE`：这就是默认的选项，级联删除，你无需显性指定它。
* `PROTECT`: 保护模式，如果采用该选项，删除的时候，会抛出 `ProtectedError` 错误。
* `SET_NULL`: 置空模式，删除的时候，外键字段被设置为空，前提就是`blank=True, null=True`,定义该字段的时候，允许为空。
* `SET_DEFAULT`: 置默认值，删除的时候，外键字段设置为默认值，所以定义外键的时候注意加上一个默认值。
* `SET()`: 自定义对应的实体的值。

**2）limit_choices_to**

该参数用于限制外键所能关联的对象，只能用于 Django 的 ModelForm（Django的表单模块）和 admin 后台，对其它场合无限制功能。

该值接受是一个字典、返回一个字典的函数

**3) db_constraint**

默认情况下，这个参数被设为 True，表示遵循数据库约束。如果设为 False，那么将无法保证数据的完整性和合法性。

**4） related_name**
用于关联对象反向引用模型的
名称。主要用于反向查询，即外键源模型实例通过管理器返回第一个模型的所有实例。

默认情况下，这个管理器的名字为 foo_set，其中 foo 是源模型名字的小写。例如：
```python
# 在终端下使用 Django
>>>b = Book.objects.get(id=1)
# 其中 entry_set 为默认的 related_name
>>>b.entry_set.all() 
>>>b.entry_set.filter(headline__contains='天龙八部')
>>>b.entry_set.count()
```

如果我们设置 related_name='novels'，那么上面的代码将变为：
```python
# 在终端下使用 Django
>>>b = Book.objects.get(id=1)
# 其中 entry_set 为默认的 related_name
>>>b.novels.all() 
>>>b.novels.filter(headline__contains='天龙八部')
>>>b.novels.count()
```

**5）related_query_name**

反向查询的关系查询集名称。用于从目标模型反向过滤模型对象的名称。具体用法如下：
```python
class Tag(models.Model):
    article = models.ForeignKey(
        Article,
        on_delete=models.CASCADE,
        related_name="tags",
        related_query_name="tag",
    )
    name = models.CharField(max_length=255)

# 现在可以使用 tag作为查询名
Article.objects.filter(tag__name="important")
```

## 3.字段选项
字段选项是给每个 Field 指定一些属性。

**db_column**： 指定当前数据库表中该字段的列名。如果没有指定，Django 默认将 Field 名作为字段名。

**db_index**： 如果赋值为 True, 将会为这个字段创建数据库索引。

**db_tablespace**：如果该字段已经设置了索引，db_tablespace 用于指定字段索引的数据库表空间的名字。另外还需要看使用的数据库支不支持表空间。如果不支持，该参数设置没有效果。

**editable**：设置该字段是否能被编辑，默认是 True。如果设为 False , 这个字段将不会出现在 admin 或者其他 ModelForm 中。 同时也会跳过 模型验证 。 

**error_messages**：用于自定义错误提示信息。参数接受的是字典类型的值。字典的 key 可以是 null, blank, invalid, invalid_choice, unique, 和 unique_for_dat 其中的一个。

**help_text**：用于前端页面上显示提示信息。要确保页面不存在 XXS 漏洞，需要使用django.utils.html.escape() 对内容进行转义。

**unique_for_date**：设置为 DateField 或者 DateTimeField 字段的名字，表示要求该字段对于相应的日期字段值是唯一的。例如，字段 title 设置了 unique_for_date="pub_date" ，那么Django将不会允许在同一 pub_date 的两条记录的 title 相同。

**unique_for_month**：用法跟 unique_for_date 类似。

**unique_for_year**：用法跟 unique_for_date 类似。

**verbose_name**：为字段设置别名。对于每一个字段类型，除了 ForeignKey、ManyToManyField和 OneToOneField 这三个特殊的关系类型，其第一可选位置参数都是 verbose_name。如果用户没有定义该选项， Django会自动将自动创建，内容是该字段属性名中的下划线转换为空格的结果。

比如这个例子中描述名是 `person's first name`:
```python
first_name = models.CharField("person's first name", max_length=30)
```
而没有主动设置时，则是 `first name`:
```python
first_name = models.CharField(max_length=30)
```

对于外键、多对多和一对一字字段，由于第一个参数需要用来指定关联的模型。

因此必须用关键字参数 verbose_name 来明确指定。如下：
```python
poll = models.ForeignKey(
    Poll,
    on_delete=models.CASCADE,
    verbose_name="the related poll",
)
sites = models.ManyToManyField(Site, verbose_name="list of sites")
place = models.OneToOneField(
    Place,
    on_delete=models.CASCADE,
    verbose_name="related place",
)
```
另外 verbose_name 不用大写首字母，在必要的时候 Django 会自动大写首字母。

**validators**：该字段将要运行的一个验证器的列表。例如 RegexValidator、EmailValidator。