
## python 用字符串调用函数或方法示例代码

### 先看一个例子：
```
>>> def foo():
    print "foo"
 
>>> def bar():
    print "bar"
 
>>> func_list = ["foo","bar"]
>>> for func in func_list:
    func()
TypeError: 'str' object is not callable
```

我们希望遍历执行列表中的函数，但是从列表中获得的函数名是字符串，所以会提示类型错误，字符串对象是不可以调用的。如果我们想要字符串变成可调用的对象呢？或是想通过变量调用模块的属性和类的属性呢？

### 以下有三种方法可以实现：

#### 1、eval()

```
>>> for func in func_list:
    eval(func)()
foo
bar
```

eval() 通常用来执行一个字符串表达式，并返回表达式的值。在这里它将字符串转换成对应的函数。eval() 功能强大但是比较危险（eval is evil），不建议使用。

#### 2、locals() 和 globals()
```
>>> for func in func_list:
    locals()[func]()
foo
bar
 
>>> for func in func_list:
    globals()[func]()
foo
bar
```
locals() 和 globals() 是python的两个内置函数，通过它们可以一字典的方式访问局部和全局变量。

#### 3、getattr()
getattr() 是 python 的内建函数，getattr(object,name) 就相当于 object.name，但是这里 name 可以为变量。

返回 foo 模块的 bar 方法
```
>>> import foo
>>> getattr(foo, 'bar')()
```
返回 Foo 类的属性
```
>>> class Foo:
  def do_foo(self):
    ...
 
  def do_bar(self):
    ...
 
>>> f = getattr(foo_instance, 'do_' + opname)
>>> f()
```



### 参考文献
https://stackoverflow.com/questions/3061/calling-a-function-of-a-module-by-using-its-name-a-string
https://docs.python.org/3/faq/programming.html#how-do-i-use-strings-to-call-functions-methods