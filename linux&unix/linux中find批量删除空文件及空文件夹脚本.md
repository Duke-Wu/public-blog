## linux中find批量删除空文件及空文件夹脚本

https://blog.csdn.net/hanglinux/article/details/49925975

[linux](http://www.111cn.net/list-156/)下批量删除空文件（大小等于0的文件）的方法

```bash
find . -name "*" -type f -size 0c | xargs -n 1 rm -f
```

用这个还可以删除指定大小的文件，只要修改对应的 -size 参数就行，例如：

```bash
find . -name "*" -type f -size 1024c | xargs -n 1 rm -f
```

就是删除1k大小的文件。（但注意不要用 -size 1k，这个得到的是占用空间1k，不是文件大小1k的）。

查询出所有的空文件夹

```bash
find -type d -empty
```

**删除文件**

列出搜索到的文件  

```bash
find . -name "shuaige.txt" -exec ls {} ;  
```

批量删除搜索到的文件 

```bash
find . -name "shuaige.txt" -exec rm -f {} ;  
```

删除前有提示 

```bash
find . -name "shuaige.txt" -ok rm -rf {} ;  
```

删除当前目录下面所有 test 文件夹下面的文件 

```bash
find . -name "test" -type d -exec rm -rf {} ; 
```

//删除文件夹下面的所有的.svn文件

```bash
find . -name '.svn' -exec rm -rf {} ;
```

注:

1.{}和之间有一个空格 
2.find . -name 之间也有空格 
3.exec 是一个后续的命令,{}内的内容代表前面查找出来的文件