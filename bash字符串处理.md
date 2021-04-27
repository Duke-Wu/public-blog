## bash字符串处理

![sf](http://img-note.wuqianlin.cn/img-md/2018-09-17-061438.jpg)



#### 一、基于模式取子符串 

- *${var#\*word}*：其中word可以是指定任意字符

  功能：自左向右，查找var变量所存储的字符串中，第一次出现的word，删除字符串开头至第一次出现word字符之间的所有字符。

  ```bash
  [root@firefly ~]# alpha=`echo {a..z} |tr -d " "`
  [root@firefly ~]# echo $alpha
  abcdefghijklmnopqrstuvwxyz
  [root@firefly ~]# echo ${alpha#*v}
  wxyz
  [root@firefly ~]# echo ${alpha#*w}
  xyz
  [root@firefly ~]# line=`getent passwd root`
  [root@firefly ~]# echo $line
  root:x:0:0:root:/root:/bin/bash
  [root@firefly ~]# echo ${line#*root}
  :x:0:0:root:/root:/bin/bash
  ```

- *${var##\*word}*：同上，贪婪模式，不同的是，删除的是字符串开头至最后一次由work指定的字符之间的所有内容 

  ```bash
  example1:
  [root@firefly ~]# echo ${line##*root} 
  :/bin/bash
  
  example2:
  [root@firefly ~]# file="var/log/messages"
  [root@firefly ~]# echo ${file#*/}
  log/messages
  [root@firefly ~]# echo ${file##*/}
  messages
  ```

- *${var%word\*}*：其中word可以是指定的任意字符；

  功能：自右而左，查找var变量所存储的字符串中，第一次出现的word，删除字符串最后一个字符向左至第一次出现word字符之间的所有字符 

  ```bash
  [root@firefly ~]# echo ${line%root*}
  root:x:0:0:root:/
  ```

- *${var%%word\*}*：同上，只不过删除字符串最右侧的字符向左直至最后一次出现word字符之间的所有字符

  ```bash
  example1:
  [root@centos7 ~]# echo ${line%%root*}
  输出为空
  
  example2:
  [root@firefly ~]# url=http://www.magedu.com:80
  [root@firefly ~]# echo ${url##*:}
  80
  [root@firefly ~]# echo ${url%%:*}
  http
  ```



#### 二、查找替换

- *${var/pattern/substr}*：查找var所表示的字符串中，第一次被pattern所匹配到的字符串，以substr替换之

  ```bash
  [root@firefly ~]# echo ${line/root/admin}
  admin:x:0:0:root:/root:/bin/bash
  ```

- *${var//pattern/substr}*：查找var所表示的字符串中，所有能被pattern所匹配到的字符串，以substr替换之

  ```bash
  [root@firefly ~]# echo ${line//root/admin}
  admin:x:0:0:admin:/admin:/bin/bash
  ```

- *${var/#pattern/substr}*：查找var所表示的字符串中，行首被pattern所匹配到的字符串，以substr替换之

  ```bash
  [root@firefly ~]# echo ${line/#root/admin}
  admin:x:0:0:root:/root:/bin/bash
  ```

- *${var/%pattern/substr}*：查找var所表示的字符串中，行尾被pattern所匹配到的字符串，以substr替换之

  ```bash
  [root@firefly ~]# echo ${line/%bash/admin}
  root:x:0:0:root:/root:/bin/admin
  ```



#### 三、查找并删除

- *${var/pattern}*：删除var所表示的字符串中第一次被pattern所匹配到的字符串

  ```bash
  [root@firefly ~]# echo ${line/root}
  :x:0:0:root:/root:/bin/bash
  ```

- *${var//pattern}*：删除var所表示的字符串中所有被pattern所匹配到的字符串

  ```bash
  [root@firefly ~]# echo ${line//root}
  :x:0:0::/:/bin/bash
  ```

- *${var/#pattern}*：删除var所表示的字符串中所有以pattern为行首所匹配到的字符串

  ```bash
  [root@firefly ~]# echo ${line/#root}
  :x:0:0:root:/root:/bin/bash
  ```

- *${var/%pattern}*：删除var所表示的字符串中有所以pattern为行尾所匹配到的字符串

  ```bash
  [root@firefly ~]# echo ${line/%bash}
  root:x:0:0:root:/root:/bin/
  ```



#### 四、字符大小写转换

- *${var^^}*：把var中的所有小写字母转换为大写

  ```bash
  [root@firefly ~]# echo ${line^^}
  ROOT:X:0:0:ROOT:/ROOT:/BIN/BASH
  [root@firefly ~]# echo ${line}
  root:x:0:0:root:/root:/bin/bash
  ```

- *${var,,}*：把var中的所有大写字母转换为小写

  ```bash
  [root@firefly ~]# str=`echo ${line^^}`
  [root@firefly ~]# echo $str
  ROOT:X:0:0:ROOT:/ROOT:/BIN/BASH
  [root@firefly ~]# echo ${str,,}
  root:x:0:0:root:/root:/bin/bash
  ```



#### 五、变量赋值

![](http://img-note.wuqianlin.cn/img-md/2018-09-17-bash%E5%8F%98%E9%87%8F%E8%B5%8B%E5%80%BC.png)

var是一个变量，str也是一个变量，expr是一个表达式，可以是字符串，这里表达的意思是当str在没有定义或定义为空或定义了，对var变量的影响。

```bash
[root@firefly ~]# var=${line-"haha"}    // 当line有值时var的值就是line的值
[root@firefly ~]# echo $var
root:x:0:0:root:/root:/bin/bash
[root@firefly ~]# line=""
[root@firefly ~]# var=${line-"haha"}    // 当line的值为空时var的值就是line的值
[root@firefly ~]# echo $var

[root@firefly ~]# unset line
[root@firefly ~]# var=${line-"haha"}    // 当line没有定义时var的值就是haha
[root@firefly ~]# echo $var 
haha
```



#### 六、高级变量用法-有类型变量

- shell变量一般是无类型的，但是bash shell提供了declare和typeset(将要淘汰)两个命令用于指定变量的类型，两个命令是等价的

- bash不支持浮点数

  ```bash
  declare [选项] 变量名 
  -r 声明或显示只读变量 
  -i 将变量定义为整型数 
  -a 将变量定义为数组 
  -A 将变量定义为关联数组 
  -f 显示已定义的所有函数名及其内容 
  -F 仅显示已定义的所有函数名 
  -x 声明或显示环境变量和函数 
  -l 声明变量为小写字母   declare  -l var=UUPER
  ```



> 参考文献

https://blog.csdn.net/itlinuxp/article/details/79060822

