# python之sys模块详解

> sys 模块包含了与解释器及其操作环境有关的变量和函数。

#### sys模块的常见变量和函数列表

- `sys.argv`: 传递给程序的命令行选项列表。argv[0] 是程序名。
- `sys.modules`: 将模块名和模块对象进行匹配的字典。
- `sys.exit([n])`: 通过引发 SystemExit 异常来退出 Python。n是一个表示退出状态码的整数退出码。0 表示正常值（默认值）；非零值表示异常。如果 n 指定为一个非整数值，则将它打印到 sys.stderr 并使用退出码 1。
- `sys.getdefaultencoding()`: 获取系统当前编码，一般默认为ascii。
- `sys.setdefaultencoding()`: 设置系统默认编码，执行dir（sys）时不会看到这个方法，在解释器中执行不通过，可以先执行reload(sys)，在执行 setdefaultencoding('utf8')，此时将系统默认编码设置为utf8。（见设置系统默认编码 ）
- `sys.getfilesystemencoding()`: 获取文件系统使用编码方式，Windows下返回'mbcs'，mac下返回'utf-8'.
- `sys.path`: 指定模块搜索路径的字符串列表，可以将写好的模块放在得到的某个路径下，就可以在程序中import时正确找到。
- `sys.platform`: 获取当前系统平台。
- `sys.stdin,sys.stdout,sys.stderr`: stdin , stdout , 以及stderr 变量包含与标准I/O 流对应的流对象. 如果需要更好地控制输出,而print 不能满足你的要求, 它们就是你所需要的. 你也可以替换它们, 这时候你就可以重定向输出和输入到其它设备( device ), 或者以非标准的方式处理它们



#### sys.argv

功能：从外部向程序内部传递参数

```python
#!/usr/bin/env python

import sys
print( sys.argv[0] )
print( sys.argv[1] )
```

运行：

```python
# python sys.py argv1
sys.py
argv1
```



#### sys.exit(n)

功能：执行到主程序末尾，解释器自动退出，但是如果需要中途退出程序，可以调用sys.exit函数，带有一个可选的整数参数返回给调用它的程序，表示你可以在主程序中捕获对sys.exit的调用。（0是正常退出，其他为异常）

示例：`exit.py`

```python
#!/usr/bin/env python

import sys

def exitfunc(value):
    print(value)
    sys.exit(0)

print("hello")

try:
    sys.exit(1)
except SystemExit as value:
    exitfunc(value)

print("come?")
```

运行：

```python
# python exit.py
hello
1
```



#### sys.path

功能：获取指定模块搜索路径的字符串集合，可以将写好的模块放在得到的某个路径下，就可以在程序中import时正确找到。

示例：

```shell
>>> import sys
>>> sys.path
['', '/Users/g/.pyenv/versions/3.6.1/lib/python36.zip', '/Users/g/.pyenv/versions/3.6.1/lib/python3.6', '/Users/g/.pyenv/versions/3.6.1/lib/python3.6/lib-dynload', '/Users/g/.pyenv/versions/3.6.1/lib/python3.6/site-packages']
```

sys.path.append("自定义模块路径")



#### sys.modules

功能：`sys.modules`是一个全局字典，该字典是python启动后就加载在内存中。每当程序员导入新的模块，`sys.modules`将自动记录该模块。当第二次再导入该模块时，python会直接到字典中查找，从而加快了程序运行的速度。它拥有字典所拥有的一切方法。

示例：`modules.py`

```python
#!/usr/bin/env python

import sys

print(sys.modules.keys())
print(sys.modules.values())
print(sys.modules["os"])
```



#### sys.stdin\stdout\stderr

功能：stdin , stdout , 以及stderr 变量包含与标准I/O 流对应的流对象. 如果需要更好地控制输出,而print 不能满足你的要求, 它们就是你所需要的. 你也可以替换它们, 这时候你就可以重定向输出和输入到其它设备( device ), 或者以非标准的方式处理它们。
