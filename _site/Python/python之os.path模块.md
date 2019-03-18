## python之os.path模块

> os.path 模块用于以一种可移植的方式操作路径名称。



### path.abspath(path)

返回路径名称 path 的绝对路径，同时将当前的工作目录考虑在内。



### path.basename(path)

返回路径名称 path 的基本名称。如果path以／或 \ 结尾，那么就会返回空值。即os.path.split( path )的第二个元素。

```bash
>>> os.path.basename('/Users/g/dev_code/hello.py/')
''
>>> os.path.basename('/Users/g/dev_code/hello.py')
'hello.py'
>>>
```



### path.dirname

返回路径名称 path  的基本名称。其实就是os.path.split(path)的第一个元素。

```bash
>>> os.path.dirname('/usr/local/python')
'/usr/local'
```



### path.exists(path)

如果 path 引用的是先有路径，返回 true。如果 path 引用的是已经损坏的符号链接，返回 False。



### path.getsize(path)

返回 path 的大小，以字节为单位。



### path.isdir(path)

如果 path 是一个存在的目录，则返回 True。否则返回 False。



### path.isfile(path)

如果 path 是一个存在的普通文件，返回 True。否则返回 False。



### os.path.split( path )   

 将path分割成目录和文件名二元组返回。

```bash
>>> os.path.split('/Users/g/simple.py')
('/Users/g', 'simple.py')
>>> 
>>> os.path.split('/Users/g/')
('/Users/g', '')
>>> 
>>> os.path.split('/Users/g')
('/Users', 'g')
```



### os.path.commonprefix( list )    

返回list的中，所有path共有的最长的路径。如果 list 为空，将返回空字符串。

```bash
>>> os.path.commonprefix(['/home/td','/home/td/ff','/home/td/fff'])
'/home/td'
>>> 
```



### os.path.isabs( path )    

如果path是绝对路径（以一个斜杆开头），返回True。



### os.path.join( path1 [,  path2 [, ...]] )    

将一个或多个路径组件智能地连接为一个路径名称。第一个绝对路径之前的参数将被忽略。

```bash
>>> os.path.join('/home/aa','/home/aa/bb','/home/aa/bb/c')
'/home/aa/bb/c'
>>> 
>>> os.path.join('home','aa','bb/c')
'home/aa/bb/c'
>>> 
```



### os.path.normcase( path )    

在Linux和Mac平台上，该函数会原样返回path，在windows平台上会将路径中所有字符转换为小写，并将所有斜杠转换为反斜杠。

```bash
\>>> os.path.normcase('c:/windows\\system32\\')

'c:\\windows\\system32\\'
```



### os.path.normpath( path )    

标准化路径名称。它将折叠多余（或冗长的）分隔符和上层引用。

```bash
>>> os.path.normpath('/usr/local/nginx/../tmp/')
'/usr/local/tmp'
>>> 
```



### os.path.getsize( path )   

 返回path的文件的大小（字节）。

```bash
>>> os.path.getsize('/Users/g/simple.py')
94
```



### os.path.splitext( path )

将路径名称拆分为基本文件名称和后缀。

```bash
os.path.splitext('c:\\csv\\test.csv')
('c:\\csv\\test', '.csv')
```



### 对文件重命名:

```python
os.rename('test.txt', 'test.py')
```



### 删掉文件:

```python
os.remove('test.py')
```



### os.walk( ) 

os.walk( )    方法用于通过在目录树种游走输出在目录中的文件名，向上或者向下。在Unix，Windows中有效。

语法

walk()方法语法格式如下：

```python
os.walk(top[, topdown=True[, onerror=None[, followlinks=False]]])
```

参数

- top -- 根目录下的每一个文件夹(包含它自己), 产生3-元组 (dirpath, dirnames, filenames)【文件夹路径, 文件夹名字, 文件名】。

- topdown --可选，为True或者没有指定, 一个目录的的3-元组将比它的任何子文件夹的3-元组先产生 (目录自上而下)。如果topdown为 False, 一个目录的3-元组将比它的任何子文件夹的3-元组后产生 (目录自下而上)。

- onerror -- 可选，是一个函数; 它调用时有一个参数, 一个OSError实例。报告这错误后，继续walk,或者抛出exception终止walk。

- followlinks -- 设置为 true，则通过软链接访问目录。

