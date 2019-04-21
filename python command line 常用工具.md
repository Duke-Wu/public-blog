# Python命令行解析argparse常用语法使用简介

python中的命令行解析最简单最原始的方法是使用sys.argv来实现，更高级的可以使用argparse这个模块。argparse从python 2.7开始被加入到标准库中，所以如果你的python版本还在2.7以下，那么需要先手动安装。

### 基本使用

```
import argparse
parser=argparse.ArgumentParser()
parser.add_argument("echo",help="echo the string")
args=parser.parse_args()
print(args.echo)
```

### 参数介绍

上面这个例子是最简单的一个使用例子，功能是把你的输入参数打印到屏幕 。不过对于基本的使用需求，这几行代码应该就已经够用，更加高级的用法可以参考官方文档。

下面介绍下例子代码。

**1、导入argparse模块**

**2、创建解析器对象ArgumentParser，可以添加参数。**

description：描述程序

​	parser=argparse.ArgumentParser(description="This is a example program ")

add_help：默认是True，可以设置False禁用

**3、add_argument()方法，用来指定程序需要接受的命令参数**

定位参数：

​	parser.add_argument("echo",help="echo the string")

可选参数：

​	parser.add_argument("--verbosity", help="increase output verbosity")

在执行程序的时候，定位参数必选，可选参数可选。

add_argument()常用的参数：

```
dest：如果提供dest，例如dest="a"，那么可以通过args.a访问该参数
default：设置参数的默认值
action：参数出发的动作
	store：保存参数，默认
	store_const：保存一个被定义为参数规格一部分的值（常量），而不是一个来自参数解析而来的值。
	store_ture/store_false：保存相应的布尔值
	append：将值保存在一个列表中。
	append_const：将一个定义在参数规格中的值（常量）保存在一个列表中。
count：参数出现的次数
parser.add_argument("-v", "--verbosity", action="count", default=0, help="increase output verbosity")
version：打印程序版本信息
type：把从命令行输入的结果转成设置的类型
choice：允许的参数值
parser.add_argument("-v", "--verbosity", type=int, choices=[0, 1, 2], help="increase output verbosity")

help：参数命令的介绍
```



如果需要更高级的用法可以参考：

<https://docs.python.org/3.6/library/parser.html>

<https://www.cnblogs.com/zknublx/p/6106343.html>

<https://zhangnq.com/2000.html>

极客学院-argparse: <http://wiki.jikexueyuan.com/project/explore-python/Standard-Modules/argparse.html>

<https://docs.python.org/2/howto/argparse.html>

<http://wiki.jikexueyuan.com/project/explore-python/Standard-Modules/argparse.html>

<https://www.jianshu.com/p/fef2d215b91d>

<https://zhuanlan.zhihu.com/p/34395749>

<https://blog.csdn.net/u012005313/article/details/50111455>

<https://medium.com/@dboyliao/python-%E8%B6%85%E5%A5%BD%E7%94%A8%E6%A8%99%E6%BA%96%E5%87%BD%E5%BC%8F%E5%BA%AB-argparse-4eab2e9dcc69>

<https://medium.com/@dboyliao/python-%E8%B6%85%E5%A5%BD%E7%94%A8%E6%A8%99%E6%BA%96%E5%87%BD%E5%BC%8F%E5%BA%AB-argparse-part-2-b5a178f08813>

<https://medium.com/@sean22492249/python-argpase-%E7%9A%84%E4%BB%8B%E7%B4%B9-python-%E7%9A%84%E5%BC%95%E6%95%B8-26d54db52b1f>





## click

Click is a Python package for creating beautiful command line interfaces in a composable way with as little code as necessary. 

<https://click.palletsprojects.com/en/7.x/>

<https://github.com/pallets/click/>

<https://palletsprojects.com/p/click/>

<https://www.jianshu.com/p/488750ca69f0>



### [docopt](http://docopt.org/)

- define the interface for your command-line app, and
- automatically generate a parser for it.

<http://docopt.org/>

