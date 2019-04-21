---
title: Python 读取文本大文件最后 X 个字节
date: 2019/3/24 23:36:25
---

# Python 读取文本大文件最后 X 个字节

## Python File seek() 方法

**seek()** 方法用于移动文件读取指针到指定位置。

语法：

```python
fileObject.seek(offset[, whence])
```

参数：

```
offset -- 开始的偏移量，也就是代表需要移动偏移的字节数
whence：可选，默认值为 0。给offset参数一个定义，表示要从哪个位置开始偏移；0代表从文件开头开始算起，1代表从当前位置开始算起，2代表从文件末尾算起。
```

返回值：该函数没有返回值。

>  python使用seek()函数的时候，报错：can't do nonzero end-relative seeks

问题代码：

```
f = open('D://demo.txt','r+')
f.write('this is a test\n')
f.seek(-3,2)
```

代码如上，会在seek()处，爆出异常：

```
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
io.UnsupportedOperation: can't do nonzero end-relative seeks
```

原因：

在文本文件中，没有使用b模式选项打开的文件，只允许从文件头开始计算相对位置，从文件尾计算时就会引发异常。

修改：

将f = open('D://demo.txt','**r+**')修改为f = open('D://demo.txt','**rb+**')即可



## txt 小文件

```python
with open("test.txt", 'r') as f:
    lines = f.readlines()
    if lines:
        first_line = lines[0]
        last_line = lines[-1]
        print(first_line)
        print(last_line)
    else:
        print("The file is empty!")
```

当文件很大时，采用这种方法资源浪费很大，可以采用下面方案。



## txt 大文件

```
with open('test.txt', 'r') as f:
    first_line = f.readline()
    off = -50
    while True:
        f.seek(off, 2) # 表示文件指针：从文件末尾(2)开始向前50个字符(-50)
        lines = f.readlines() # 读取文件指针范围内所有行
        if len(lines)>=2: # 判断是否最后至少有两行，这样保证了最后一行是完整的
            last_line = lines[-1] # 取最后一行
            break
        #如果off为50时得到的readlines只有一行内容，那么不能保证最后一行是完整的
        #所以off翻倍重新运行，直到readlines不止一行
        off *= 2

    print('文件' + fname + '第一行为：' + first_line）
    print('文件' + fname + '最后一行为：'+ last_line）
```

http://www.cnblogs.com/zhangchenliang/p/8136958.html