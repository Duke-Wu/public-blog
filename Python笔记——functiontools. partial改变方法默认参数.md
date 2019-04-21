# functiontools. partial 的使用技巧



functools.partial 可以用来改变一个方法默认参数
1. 改变原有默认值参数的默认值
2. 给原来没有默认值的参数增加默认值

```bash
In [20]: def foo(a, b=0):
    ...:     '''
    ...:     int add
    ...:     '''
    ...:     print(a + b)
    ...:     
In [21]: foo(1)
1
In [22]: foo(1,1)
2
In [23]: import functools
In [24]: foo1 = functools.partial(foo, b=5)
In [25]: foo1(1)
6
In [26]: foo2 = functools.partial(foo, a=10)
In [27]: foo2()
10
```

**foo2** is a partial object, it only has three read-only attributes, list them below:

```
print(foo2.func)
print(foo2.args)
print(foo2.keywords)
print(dir(foo2))

```
默认情况下 partial 对象是没有 __name__ __doc__ 属性，使用 update_wrapper 从原始方法中添加属性到partial 对象中
```bash
In [32]: print(foo2.__doc__)
partial(func, *args, **keywords) - new function with partial application
    of the given arguments and keywords.
```

修改为foo的文档信息了

```
In [33]: functools.update_wrapper(foo2, foo)
Out[33]: functools.partial(<function foo at 0x110eb9400>, a=10)
In [34]: print(foo2.__doc__)
    int add
```

