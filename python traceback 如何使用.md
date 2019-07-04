## python traceback 如何使用

1. traceback.print_exc( ) 是对异常栈输出
  
2. traceback.format_exc( ) 是把异常栈以字符串的形式返回，print(traceback.format_exc( )) 就相当于traceback.print_exc( )。
  
3. traceback.print_exception( )

   traceback.print_exc( ) 实现方式就是traceback.print_exception(sys.exc_info( ))，可以点sys.exc_info( )进去看看实现。



#### 测试代码如下：

```python
def func(a, b):
    return a / b
    
    
if __name__ == '__main__':
    import sys
    import time
    import traceback

    try:
        func(1, 0)
    except Exception as e:
        print('***', type(e), e, '***')
        time.sleep(2)

        print("***traceback.print_exc():*** ")
        time.sleep(1)
        traceback.print_exc()
        time.sleep(2)

        print("***traceback.format_exc():*** ")
        time.sleep(1)
        print(traceback.format_exc())
        time.sleep(2)

        print("***traceback.print_exception():*** ")
        time.sleep(1)
        traceback.print_exception(*sys.exc_info())

```

运行结果：

```
*** <class 'ZeroDivisionError'> division by zero ***
***traceback.print_exc():*** 
Traceback (most recent call last):
  File "/Users/g/Desktop/code/compile_helper/demos/track_back.py", line 11, in <module>
    func(1, 0)
  File "/Users/g/Desktop/code/compile_helper/demos/track_back.py", line 2, in func
    return a / b
ZeroDivisionError: division by zero
***traceback.format_exc():*** 
Traceback (most recent call last):
  File "/Users/g/Desktop/code/compile_helper/demos/track_back.py", line 11, in <module>
    func(1, 0)
  File "/Users/g/Desktop/code/compile_helper/demos/track_back.py", line 2, in func
    return a / b
ZeroDivisionError: division by zero

***traceback.print_exception():*** 
Traceback (most recent call last):
  File "/Users/g/Desktop/code/compile_helper/demos/track_back.py", line 11, in <module>
    func(1, 0)
  File "/Users/g/Desktop/code/compile_helper/demos/track_back.py", line 2, in func
    return a / b
ZeroDivisionError: division by zero
```

可以看出，三种方式打印结果是一样的。在开发时，做调试是很方便的。也可以把这种异常栈写入日志。

```
logging.exception(ex)

# 指名输出栈踪迹, logging.exception的内部也是包了一层此做法
logging.error(ex, exc_info=1) 

# 更加严重的错误级别 
logging.critical(ex, exc_info=1) 

# 我直接copy的，未尝试。有时间会试下的
```

python 还有一个模块叫cgitb，输出的error非常详情。

```
    try:
        func(1, 0)
    except Exception as e:
        import cgitb
        cgitb.enable(format='text')
        func(1, 0)
```



https://www.jianshu.com/p/01ed4b8d7d9a

https://blog.csdn.net/lengxingxing_/article/details/56317838

https://www.cnblogs.com/shangpolu/p/7206348.html


