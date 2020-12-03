# python 拷贝函数签名

使用场景有很多，比如C API在Python下很多都变成了(*args, **kwargs)的参数，这时候可能需要为其添加一个更严格签名来约束参数。

查了许多资料，能有效的拷贝函数签名貌似只能通过动态创建具有新签名的包装函数。使用这一技术的典型范例就是decorator库：<https://pypi.python.org/pypi/decorator>



下面就根据decorator来实现一个拷贝函数签名的decorator：

```python
from decorator import FunctionMaker, decorator
from contextlib import contextmanager
  
@contextmanager
def rename_scope(obj, new_name):
    """ A rename scope to rename obj's __name__, restore __name__ when exit. """
    old_name = obj.__name__
    obj.__name__ = new_name
    yield
    obj.__name__ = old_name
  
def newcaller(caller, sig_func):
    """ Generate a new caller using sig_func signatures, doc and name, etc. """
    evaldict = sig_func.func_globals.copy()
    evaldict['_call_'] = caller
    return FunctionMaker.create(
        sig_func, "return _call_(%(shortsignature)s)",
        evaldict, signature=sig_func)
  
  
def signature(sig_func, copy_name=False):
    """ A decorator to update function signature, doc by sig_func. If copy_name, the return function use sig_func name else function keep its name. """
    def sig_wrapper(caller):
        with rename_scope(sig_func, sig_func.__name__ if copy_name else caller.__name__):
            return newcaller(caller, sig_func)
    return sig_wrapper
```



用起来大概是这一个样子：
- 首先，函数签名肯定是变了，在IPython下立马能看到函数提示变化：
![](http://img-note.wuqianlin.cn/img-md/2019-03-28-095410.jpg)


- 其次，是函数调用参数符合签名时，正常使用：
  ![](http://img-note.wuqianlin.cn/img-md/2019-03-28-095740.jpg)


- 最后，是函数调用参数不符合签名时，会报详细错误：
![](http://img-note.wuqianlin.cn/img-md/2019-03-28-095814.jpg)

目前实现的signature能够控制是否拷贝签名函数的函数名，对于doc也可以实现类似的可控制的参数。