# 类的私有变量和私有方法

在Python中可以通过在属性变量名前加上双下划线定义属性为私有属性

## 特殊变量命名

1. **`_xx`** 以单下划线开头的表示的是`protected`类型的变量。即保护类型只能允许其本身与子类进行访问。若内部变量标示，如： 当使用`from M import`时，不会将以一个下划线开头的对象引入 。

2. **`__xx`** 双下划线的表示的是私有类型的变量。只能允许这个类本身进行访问了，连子类也不可以用于命名一个类属性（类变量），调用时名字被改变（在类FooBar内部，`__boo`变成`_FooBar__boo`,如`self._FooBar__boo`）

3. **`__xx__`**定义的是特列方法。用户控制的命名空间内的变量或是属性，如`__init__` , `__import__`或是`__file__` 。只有当文档有说明时使用，不要自己定义这类变量。 （就是说这些是python内部定义的变量名）

在这里强调说一下私有变量，`python`默认的成员函数和成员变量都是公开的,没有像其他类似语言的`public`,`private`等关键字修饰。但是可以在变量前面加上两个下划线**`_`**，这样的话函数或变量就变成私有的。这是`python`的私有变量轧压(这个翻译好拗口)，英文是(`private name mangling.`) **情况就是当变量被标记为私有后,在变量的前端插入类名,再类名前添加一个下划线`_`,即形成了_ClassName__变量名.**



Python内置类属性
`__dict__` : 类的属性（包含一个字典，由类的数据属性组成）
`__doc__` :类的文档字符串
`__module__`: 类定义所在的模块（类的全名是`__main__.className`，如果类位于一个导入模块`mymod`中，那么`className.__module__` 等于 `mymod`）
`__bases__` : 类的所有父类构成元素（包含了一个由所有父类组成的元组）



```python
class pub():
    _name = 'protected类型的变量'
    __info = '私有类型的变量'
    def _func(self):
        print("这是一个protected类型的方法")
    def __func2(self):
        print('这是一个私有类型的方法')
    def get(self):
        return(self.__info)

a = pub()
print(a._name)
a._func()
# print(a.info)
# 执行结果：
# protected类型的变量
# 这是一个protected类型的方法

# protected类型的变量和方法 在类的实例中可以获取和调用

# # print(a.__info)
# # a.__func2()
# 执行结果：
#   File "D:/Python/class/class3.py", line 46, in <module>
#     print(a.__info)
# # AttributeError: pub instance has no attribute '__info'
#     a.__func2()
# AttributeError: pub instance has no attribute '__func2'

# 私有类型的变量和方法 在类的实例中获取和调用不到

# 获取私有类型的变量

print(a.get())
# 执行结果：私有类型的变量
# 如果想要在实例中获取到类的私有类形变量可以通过在类中声明普通方法，返回私有类形变量的方式获取


print(dir(a))
# 执行结果：['__doc__', '__module__', '_func', '_name', '_pub__func2', '_pub__info', 'get']
print(a.__dict__)
# 执行结果：{}
print(a.__doc__)
# 执行结果： None
print(a.__module__)
# 执行结果：__main__
print(a.__bases__)
# 执行结果:
#     print(a.__bases__)
# AttributeError: pub instance has no attribute '__bases__'
```

