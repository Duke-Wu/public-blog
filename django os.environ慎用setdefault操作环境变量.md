---
title: django os.environ慎用setdefault操作环境变量
date: 2019/4/1 14:17:25
---

## django os.environ慎用setdefault操作环境变量


在绝大多数情况下，如果需要在程序运行过程中设置环境变量，使用os.environ.setdefault函数是没有任何问题的，但是有两种场景下setdefault会造成意外的问题，需要慎用：

1. 如果程序执行前，系统里已经存在了某环境变量（如ENV=VAL1），此时如果在程序中用setdefault函数对该环境变量设置另一个不同的值（如VAL2），会因为setdefault函数的特性导致无法设置为新值。
2. 也是因为上述这一点，如果进程A先设置了环境变量（如ENV=VAL1），而A启动了子进程B，子进程B会继承A进程的所有环境变量，会导致B运行的时候，程序运行环境里已经存在环境变量ENV，如果此时用setdefault函数对该环境变量设置另一个不同的值（如VAL2），也会因为同样的原因导致无法设置为新值。

因此，在程序运行中设置系统环境变量的最安全方法还是：

```
os.environ['ENV'] = 'VAL'
```

```
if "EVN_KEY" in os.environ: 判断环境变量是否已定义
```


#### 设置系统环境变量

1. `os.environ['环境变量名称']='环境变量值'  # 其中key和value均为string类型`
2. `os.putenv('环境变量名称', '环境变量值')`

#### 获取系统环境变量

1. `os.environ['环境变量名称']`
2. `os.getenv('环境变量名称')`

