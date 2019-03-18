## Bash 字符串切片

- *${#var}* : 返回字符串变量var的长度

  ```bash
  [root@firefly ~]# alpha=`echo {a..z} | tr -d " "`  // 创建一个变量将26个字母赋值进去，并且不要空格
  [root@firefly ~]# echo $alpha
  abcdefghijklmnopqrstuvwxyz
  [root@firefly ~]# echo ${#alpha}
  26
  ```

- *\${var:offset}*：返回字符串变量var中从第offset个字符后（不包括第offset个字符）的字符开始，到最后的部分，offset的取值在0到${#var}-1之间（bash4.2后，允许为负值）

  ```bash
  [root@firefly ~]# echo ${alpha:3}    // 跳过前3个显示后面全部
  defghijklmnopqrstuvwxyz
  ```

- *${var:offset:number}*：返回字符串变量var中从第offset个字符后（不包括第offset个字符）的字符开始，长度为number的部分

  ```bash
  [root@firefly ~]# echo ${alpha:3:4}
  defg
  ```

- *${var: -length}*：取字符串的最右侧几个字符（ *注意：冒号后必须有一个空白字符*）

  ```bash
  [root@firefly ~]# echo ${alpha: -3}
  xyz
  ```

- *${var:offset:-length}*： 从最左侧跳过*offset*字符，一直向右取到距离最右侧length个字符之前的内容

  ```bash
  [root@centos6 ~]# echo ${alpha:3:-4}   // centos6不支持这种写法
  -bash: -4: substring expression < 0
  [root@centos7 ~]# echo ${alpha:3:-4}  // 7上是可以的，取前3个到倒数第4个之间的
  defghijklmnopqrstuv
  ```

- *${var: -length:-offset}*：先从最右侧向左取到length个字符开始，在向右取到距离最右侧offset个字符之间的内容 （注意：-length前的空格）

  ```bash
  [root@centos7 ~]# echo ${alpha: -3:-2}  // 取倒数3个但是最后又2个不要
  x
  [root@centos7 ~]# echo ${alpha: -5:-2}  // 前面的数字一定要比后面的数字大
  vwx
  ```

