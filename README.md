# CodeReview

## Table of Contents
1. [前言](#1-前言)
   - [1.1. 一般信息[重要必读]](#11-一般信息重要必读)
   - [1.2. 如何使用本编程规范](#12-如何使用本编程规范)
     - [1.2.1. 本规范的层次结构](#121-本规范的层次结构)
     - [1.2.2. 条目的级别和编号](#122-条目的级别和编号)
   - [1.3. The Zen of Python](#13-the-zen-of-python)
2. [语言规范](#2-语言规范)
   - [2.1. import](#21-import)
   - [2.2. 异常](#22-异常)
   - [2.3. 全局变量](#23-全局变量)
   - [2.4. 构造函数](#24-构造函数)
   - [2.5. 函数返回值](#25-函数返回值)
   - [2.6. 嵌套/局部/内部类或函数](#26-嵌套局部内部类或函数)
   - [2.7. 列表推导](#27-列表推导)
   - [2.8. 默认迭代器和操作符](#28-默认迭代器和操作符)
   - [2.9. 生成器](#29-生成器)
   - [2.10. lambda函数](#210-lambda函数)
   - [2.11. 条件表达式](#211-条件表达式)
   - [2.12. 参数](#212-参数)
   - [2.13. 属性(properties)](#213-属性properties)
   - [2.14. True/False求值](#214-truefalse求值)
   - [2.15. 函数和方法装饰器](#215-函数和方法装饰器)
3. [风格规范](#3-风格规范)
   - [3.1. 分号](#31-分号)
   - [3.2. 行列长度](#32-行列长度)
   - [3.3. 括号](#33-括号)
   - [3.4. 缩进](#34-缩进)
   - [3.5. 空行](#35-空行)
   - [3.6. 空格](#36-空格)
   - [3.7. 注释](#37-注释)
   - [3.8. import格式](#38-import格式)
   - [3.9. 命名规则](#39-命名规则)
4. [编程实践](#4-编程实践)
   - [4.1. python解释器](#41-python解释器)
   - [4.2. 文件编码](#42-文件编码)
   - [4.3. 类继承](#43-类继承)
   - [4.4. 字符串格式化与拼接](#44-字符串格式化与拼接)
   - [4.5. 文件和 socket](#45-文件和-socket)
   - [4.6. 主程序](#46-主程序)
   - [4.7. 单元测试](#47-单元测试)
   - [4.8. 日志输出](#48-日志输出)
5. [工具支持](#5-工具支持)

## 1. 前言

### 1.1. 一般信息[重要必读]
此编码风格指南主要基于 Google Python Style Guide ，结合python使用习惯和实际开发情况制定。
这份文档存在的意义是让大家写出统一风格的代码，让我们的模块可维护性和可读性更好；
文档内容可能会与您的喜好冲突， 请尽量用包容的心态来接受; 

### 1.2. 如何使用本编程规范

#### 1.2.1. 本规范的层次结构
本规范可分为三大部分，分别对Python语法、风格、编程实践作出规定与建议。
每一部分有若干专题，每一专题下有若干条目。
条目是规范的基本组成部分，每一条目由规定、定义、解释、示例、参考等项组成。

#### 1.2.2. 条目的级别和编号
本规范的条目分两个级别:
* [强制]：要求所有程序必须遵守，不得违反
* [建议]：建议遵守，除非确有特殊情况

### 1.3. The Zen of Python
建议每位python开发人员细读"python之禅"，理解规范背后的思想

```
The Zen of Python Beautiful is better than ugly. Explicit is better than implicit. Simple is better than complex. Complex is better than complicated. Flat is better than nested. Sparse is better than dense. Readability counts. Special cases aren't special enough to break the rules. Although practicality beats purity. Errors should never pass silently. Unless explicitly silenced. In the face of ambiguity, refuse the temptation to guess. There should be one-- and preferably only one --obvious way to do it. Although that way may not be obvious at first unless you're Dutch. Now is better than never. Although never is often better than |right| now. If the implementation is hard to explain, it's a bad idea. If the implementation is easy to explain, it may be a good idea. Namespaces are one honking great idea -- let's do more of those!-- by Tim Peters
```

## 2. 语言规范

### 2.1. import
- [建议] 禁止使用from xxx import yyy语法直接导入类或函数(即yyy只能是module或package，不能是类或函数)。
  
  **解释**  
  避免冲突。调用关系简单明了，x.obj表示obj对象定义在模块x中。
  
  **示例**  
  ```python
  # YES
  from Crypto.Cipher import AES
  import os
  os.unlink(path)
  
  # NO
  from os import unlink
  unlink(path)
  ```

- [建议] [PY002] 禁止使用from xxx import *
- [建议] [PY003] import时必须使用package全路径名(相对PYTHONPATH)，禁止使用相对路径(相对当前路径)，禁止使用sys.path.append('../../')等类似操作改变当前环境变量。

### 2.2. 异常
- [建议] 可以使用异常。但使用前请务必详细了解异常的行为，谨慎使用
- [强制] [PY004] 禁止使用双参数形式（raise MyException, 'Error Message'）或字符串形式（raise 'Error Message'）语法抛异常。
- [强制] 如果需要自定义异常，应该在模块内定义名为Error的异常基类。该基类必须继承自Exception。其它异常类都从该Error类派生而来。
- [强制] 除非重新抛出异常，禁止使用except:捕获所有异常。
- [建议] 除非重新抛出异常，否则不建议捕获Exception或StandardError。如果捕获，必须在日志中记录所捕获异常信息。
- [强制] [PY007] 捕捉异常时，应当使用as语法，禁止使用逗号语法。
- [建议] 建议try中的代码尽可能少。避免catch住未预期的异常，掩藏掉真正的错误。
- [建议] 建议使用finally子句来执行那些无论try块中有没有异常都应该被执行的代码，这对于清理资源常常很有用。例如：文件关闭。

### 2.3. 全局变量
- [强制] 禁止使用全局变量。除了以下例外：脚本默认参数模块级常量
- [强制] 如果定义全局变量，必须写在文件头部。

### 2.4. 构造函数
- [建议] 类构造函数应该尽量简单，不能包含可能失败或过于复杂的操作

### 2.5. 函数返回值
- [强制] [PY004] 函数返回值必须小于等于3个。3个以上时必须通过class/namedtuple/dict等具名形式进行包装

### 2.6. 嵌套/局部/内部类或函数
- [建议] 不推荐使用嵌套/局部/内部类或函数

### 2.7. 列表推导
- [建议] [PY011] 可以使用列表推导。mapping、loop、filter部分单独成行，且最多只能写一行。禁止多层loop或filter。

### 2.8. 默认迭代器和操作符
- [强制] 对容器或文件的只读遍历，应该使用内置的迭代方法，不要使用返回list的方式遍历。
- [强制] [PY013] 对容器类型，使用in或not in判断元素是否存在。而不是has_key。

### 2.9. 生成器
- [建议] 当返回较长列表数据时建议使用yield和generator函数。

### 2.10. lambda函数
- [建议] [PY014] 可以使用lambda函数，但仅限一行之内。

### 2.11. 条件表达式
- [建议] 条件表达式仅用于一行之内，禁止嵌套使用

### 2.12. 参数
#### 2.12.1 默认参数
- [建议] [PY016] 仅可使用以下基本类型字面常量或常量作为默认参数：整数、bool、浮点、字符串、None

#### 2.12.2 可变参数、关键字参数
- [建议] 善于使用可变参数（*args）、关键字参数（**kwargs），将使你的代码更加简洁

### 2.13. 属性(properties)
- [强制] 可以使用property。但禁止在派生类里改写property实现。

### 2.14. True/False求值
- [建议] 建议显式转换到bool类型，慎用到bool类型的隐式转换。如使用隐式转换，你需要确保充分了解其语义
- [强制] [PY018] 禁止使用==或!=判断表达式是否为None，应该用is或is not None
- [强制] 当明确expr为bool类型时，禁止使用==或!=与True/False比较。应该替换为expr或not expr
- [强制] 判断某个整数表达式expr是否为零时，禁止使用not expr，应该使用expr == 0

### 2.15. 函数和方法装饰器
- [建议] 建议仅在以下几种情况下使用函数方法装饰器。其它情况下如有明显好处，且不影响代码可维护性，可以谨慎使用
  - @property、@classmethod、@staticmethod
  - 自动化测试
  - 第三方库要求使用的装饰器

## 3. 风格规范

### 3.1. 分号
- [建议] [PY021] 禁止以分号结束语句
- [建议] [PY022] 一行只能写一条语句，没有例外情况

### 3.2. 行列长度
- [强制] [PY023] 每行不得超过120个字符
- [强制] [PY024] 函数长度不得超过120行

### 3.3. 括号
- [建议] 除非用于明确算术表达式优先级、tuple或者隐式行连接，否则尽量避免冗余的括号。

### 3.4. 缩进
- [强制] 使用4个空格缩进，禁止使用tab缩进。
- [强制] 把单行内容拆成多行写时，要么与首行保持对齐；要么首行留空，从第二行起统一缩进４个空格；为与后面的代码区分，可以使用8空格缩进。

### 3.5. 空行
- [建议] [PY027] 文件级定义(类或全局函数)之间隔两个空行，类方法之间隔一个空行

### 3.6. 空格
- [建议] [PY028] 圆括号、方括号、花括号内侧都不加空格
- [建议] [PY029] 参数列表, 索引或切片的左括号前不应加空格
- [强制] [PY030] 逗号、分号、冒号前不加空格，后边加一个空格
- [强制] [PY031] 所有二元运算符前后各加一个空格
- [强制] [PY032] 关键字参数或参数默认值里的等号前后不加空格

### 3.7. 注释
- [强制] [PY033] 使用docstring描述module、function、class和method接口。docstring必须用三个双引号括起来。
- [强制] 对外接口部分必须用docstring描述，内部接口视情况自行决定是否写docstring。
- [强制][PY034] 接口的docstring描述至少包括功能简介、参数、返回值。如果可能抛出异常，必须注明。
- [强制] 每个文件都必须有文件声明，文件声明必须包括以下信息：版权声明，功能和用途简介，修改人及联系方式。
- [建议] TODO注释格式必须为：
  ```
  # TODO: 干什么事情$负责人(邮箱前缀)$最终期限(YYYY-MM-DD)$
  ```

### 3.8. import格式
- [建议] [PY037] 每行只能导入一个库
- [建议] 必须按如下顺序排列import，每部分之间留一个空行：标准库, 第三方库, 应用程序自有库

### 3.9. 命名规则
- [强制] [PY039] 类(包括异常)名使用首字母大写驼峰式命名
- [强制] 常量使用全大写字母，单词间用下划线分隔
- [强制] 其它情况(目录/文件/package/module/function/method/variable/parameter)一律使用全小写字母，单词间用下划线分隔
- [建议] protected成员使用单下划线前缀，private成员使用双下划线前缀
- [建议] [PY043] 禁止使用双下划线开头，双下划线结尾的名字(类似__init__)

## 4. 编程实践

### 4.1. python解释器
- [建议] 模块的主程序必须以#!/usr/bin/env python开头。如果明确只支持某个python版本，请带上python版本号
- [建议] 模块可以自带某个特定版本的python环境一起发布。需要在程序的启动脚本中指定具体使用的python解释器程序
- [建议] 推荐使用2.7版本(含)以上的python解释器

### 4.2. 文件编码
- [强制] 如果文件包含非ASCII字符，必须在文件前两行标明字符编码。
- [强制] 只能使用UTF-8或GB18030编码。推荐使用UTF-8编码，如果项目确有需要，可以使用GB18030

### 4.3. 类继承
- [建议] [PY046] 如果一个类没有基类，必须继承自object类。

### 4.4. 字符串格式化与拼接
- [建议] 除了a+b这种最简单的情况外，应该使用%或format格式化字符串。
- [建议] 不要使用+=拼接字符串列表，应该使用join。但需确保列表中全是Strings类型，如果有Numbers等其它类型，会报TypeError错误，当不确定时建议仍使用+=拼接字符串。

### 4.5. 文件和 socket
- [建议] 用完文件或socket后必须显式关闭句柄。建议使用with语法简化开发

### 4.6. 主程序
- [建议] 所有module都必须可导入。如需要执行主程序，必须检查`__name__ == '__main__'`

### 4.7. 单元测试
- [建议] 推荐使用UnitTests做单元测试。是否需要做单元测试以及目标单测覆盖率由项目负责人自行决定。
- [建议] 推荐测试代码放在单独的test目录中。如果被测试代码文件名为xxx.py，那么测试代码文件应该被命名为xxx_test.py

### 4.8. 日志输出
- [建议] 推荐使用python自带的logging库打印日志。
- [建议] 推荐默认日志格式："%(levelname)s: %(asctime)s: %(filename)s:%(lineno)d * %(thread)d %(message)s", 时间格式："%Y-%m-%d%H:%M:%S"
- [建议] 推荐线上程序使用两个日志文件：一个专门记录warning/error/critical日志，另一个记录所有日志。

## 5. 工具支持
对所有标记的有"eagle支持"的规则，大家可以本地调用客户端检查代码是否符合这部分规则，另外在发起代码评审（cooder）时也将自动触发检查，结果会以行间评论的形式插入到代码评审中，帮助作者检查代码、帮助评审人评审代码，详细内容点击查看
