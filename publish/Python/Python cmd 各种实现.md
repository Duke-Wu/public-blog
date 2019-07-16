---
title: Python cmd 各种实现
date: 2019/1/1 0:0:0
updated: 2019/7/4 14:40:25
comments: true
tags:
- python
categories:
- tool kits
---

## Python cmd 各种实现

### 1. 使用 os.system("cmd")

```python
os.system("ls")
```



### 2. subprocess.Popen

Popen方法不会打印出cmd在linux上执行的信息。的确，Popen非常强大，支持多种参数和模式。使用前需要from subprocess import Popen, PIPE。但是Popen函数有一个缺陷，就是它是一个阻塞的方法。如果运行cmd时产生的内容非常多，函数非常容易阻塞住。解决办法是不使用wait()方法，但是也不能获得执行的返回值了。

```python
subprocess.Popen(args, bufsize=0, executable=None, stdin=None, stdout=None, stderr=None, preexec_fn=None, close_fds=False, shell=False, cwd=None, env=None, universal_newlines=False, startupinfo=None, creationflags=0)
```



### 3. 使用 commands.getstatusoutput 方法

这个方法也不会打印出cmd在linux上执行的信息。这个方法唯一的优点是，它不是一个阻塞的方法。即没有Popen函数阻塞的问题。使用前需要import commands。

```python
status, output = commands.getstatusoutput("ls")
```