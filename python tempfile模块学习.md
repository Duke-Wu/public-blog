#### 一、tempfile.mkstemp([suffix=”[, prefix=’tmp'[, dir=None[, text=False]]]])

说明：该方法仅仅创建一个临时文件；返回包含两个元素的元组，第一个元素指操作该临时文件的安全级别，第二个元素指该临时文件的路径。

1. 参数suffix和prefix分别表示临时文件名称的后缀和前缀；
2. 参数dir指定了临时文件所在的目录，如果没有指定目录，将根据系统环境变量TMPDIR, TEMP或者TMP的设置来保存临时文件；
3. 参数text指定了是否以文本的形式来操作文件，默认为False，表示以二进制的形式来操作文件。



#### 二、tempfile.TemporaryFile([mode=’w+b'[, bufsize=-1[, suffix=”[, prefix=’tmp'[, dir=None]]]]])
说明：该方法返回一个文件对象用于临时数据保存，它没有文件名字，你也找不到它所在的位置；当文件对象被close或者被del的时候，临时文件将从磁盘上删除。

1. 参数mode、bufsize的用法与open()函数一样；
2. 其他参数同上
```
temp = tempfile.TemporaryFile()
try:
    print 'temp:', temp
    print 'temp.name:', temp.name
    ＃ 写入
    temp.write('hello world\n')
    temp.seek(0)
    # 读取
    print 'temp.read:', temp.read()
finally:
    # 自动清除文件
    temp.close()　　
```

＃ 输出结果
```
temp: <open file '<fdopen>', mode 'w+b' at 0x1004486f0>
temp.name: <fdopen> ＃ 文件没有名字
temp.read: hello world
```



#### 三、tempfile.NamedTemporaryFile([mode=’w+b'[, bufsize=-1[, suffix=”[, prefix=’tmp'[, dir=None[, delete=True]]]]]])
 说明：同tempfile.TemporaryFile类似，主要区别就是多了个delete参数，用于指定文件对象close或者被del之后，是否也一同删除磁盘上的临时文件（当delete = True的时候，即默认的时候行为与TemporaryFile一样）。

```
temp = tempfile.NamedTemporaryFile(suffix='.sh', prefix='script_', dir='/tmp')
try:
    temp.write('aaaa\n')
    # 指定从什么位置写入
    temp.seek(0)  
    print temp.read()
finally:
    # 自动清除文件，因为delete参数默认是True
    temp.close()  
```

*注：seek有三种写入模式：seek(offset,where): where=0从起始位置移动，1从当前位置移动，2从结束位置移动。*



#### 四、tempfile.SpooledTemporaryFile([max_size=0[, mode=’w+b'[, bufsize=-1[, suffix=”[, prefix=’tmp'[, dir=None]]]]]])
 说明：同tempfile.TemporaryFile类似，不同的是向文件对象写数据的时候，数据长度只有到达参数max_size指定大小时，或者调用类文件对象的fileno()方法，数据才会真正写入到磁盘的临时文件中。



#### 五、tempfile.mkdtemp([suffix=”[, prefix=’tmp'[, dir=None]]])
 说明：方法用于创建一个临时文件夹，它返回临时文件夹的绝对路径。

文章参考自：
 [Python模块学习：tempfile 临时文件(夹)操作](https://link.jianshu.com?t=http://python.jobbole.com/81515/)


作者：huozhihui
链接：https://www.jianshu.com/p/9bde8afc2ec8
来源：简书
简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。
