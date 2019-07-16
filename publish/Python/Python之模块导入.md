---
title: Python标准库系列之模块介绍
date: 2019/1/1 0:0:0
updated: 2019/7/4 14:40:25
comments: true
tags:
- python
categories:
- tool kits
---

## Python标准库系列之模块介绍

> Python的模块其实就是封装了一个或者多个功能的代码集合，以便于重用，模块可以是一个`文件`也可以是一个`目录`，目录的形式称作`包`。

## 模块分类

#### **内置模块**

内置模块可以理解成当你安装好python环境之后，直接可以使用`import`导入的就是内置模块，默认模块路径为：`C:Python35lib`，你也可以通过以下方式获取内置模块的路径：

```python
# 导入sys模块
 >>> import sys
 # 最后一个目录就是内置模块的路径
>>> for n in sys.path:
...    print(n)
...    

/Users/g/.pyenv/versions/3.6.1/bin
/Users/g/.pyenv/versions/3.6.1/lib/python36.zip
/Users/g/.pyenv/versions/3.6.1/lib/python3.6
/Users/g/.pyenv/versions/3.6.1/lib/python3.6/lib-dynload
/Users/g/.pyenv/versions/3.6.1/lib/python3.6/site-packages
/Users/g/.pyenv/versions/3.6.1/lib/python3.6/site-packages/IPython/extensions
/Users/g/.ipython
```

#### **第三方模块**

第三方模块通常是开发者自己编写的模块，然后提交到python官方的库中，这样我们就可以下载安装使用了，默认安装目录为`C:Python35libsite-packages`，

#### **自定义模块**

自己编写的模块

## 模块的导入方式

把一个模块当做成一个整体来进行导入

```python
import sys
```

从一个模块中导入特定的变量或者是方法

```python
from sys import path
```

调用的时候直接使用方法名`path`

```bash
>>> path
['', '/Users/g/.pyenv/versions/3.6.1/lib/python36.zip', '/Users/g/.pyenv/versions/3.6.1/lib/python3.6', '/Users/g/.pyenv/versions/3.6.1/lib/python3.6/lib-dynload', '/Users/g/.pyenv/versions/3.6.1/lib/python3.6/site-packages']
```

添加默认的环境变量,当前生效

```bash
sys.path.append("PATH_NAME")
```

可以使用`imp`模块中的`reload`方法重新载入某个模块的方法，例如下面的实例：

```python
$ cat simple.py 
#!/use/bin/env python
 
print('Hello, World!')
spam = 1
```

```bash
>>> import simple
Hello, World!
>>> simple.spam
1
>>> simple.spam += 1
>>> import simple
>>> simple.spam
2
>>> import imp
>>> imp.reload(simple)
Hello, World!
 
>>> simple.spam
1
```



## 模块导入顺序

1. 先在当前脚本目录寻找有没有与导入模块名称相同的文件，如果有就把这个文件当作模块导入（据不完全统计，这是个坑，测试`re`模块没有问题，但是测试`sys`模块就有问题了）
2. 查找模块路径下面有没有对应的模块名
3. 如果没有找到模块名就报错



## import是如何工作的？

模块在被导入的时候会执行以下三个步骤：

1. 通过环境变量找到模块文件；
2. 编译成字节码文件，如果有字节码文件则导入字节码文件；
3. 执行模块中的代码来创建所定义的对象；

以上的三个步骤只有在程序运行时，模块被第一次导入时才会进行。如果已经导入了这个模块然后再次导入的时候会跳过上面的三个步骤，它会直接提取内存中已经加载的模块对象。Python已经导入的模块会保存在`sys.modules`字典中。



## _X与__all__

在模块中的所有变量以`_`开头的都不会被`from *`所导入

```python
$ cat simple.py 
#!/use/bin/env python

_spam1 = 1
spam2 = 1
```

```bash
>>> from simple import *
>>> dir()
# _spam1没有被导入
['__builtins__', '__doc__', '__name__', '__package__', 'spam2']
```

相反的`__all__`列表里面的变量则会被`from *`所导入，没有在`__all__`列表里面的变量则不会被导入

```bash
$ cat simple.py
#!/use/bin/env python
 
__all__ = ['spam2']
 
spam1 = 1
spam2 = 1
```

```bash
>>> from simple import *
>>> dir()
# spam1没有被导入
['__builtins__', '__doc__', '__name__', '__package__', 'spam2']
```


