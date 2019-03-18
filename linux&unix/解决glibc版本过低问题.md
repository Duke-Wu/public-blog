### 解决`libc.so.6: version 'GLIBC_2.14' not found`问题

转载自：https://blog.csdn.net/cpplang/article/details/8462768

1. 试图运行程序，提示`"libc.so.6: version 'GLIBC_2.14' not found"`，原因是系统的glibc版本太低，软件编译时使用了较高版本的glibc引起的。

2. 查看系统glibc支持的版本：

   ```bash
   strings /lib64/libc.so.6 |grep GLIBC_` 
   rpm -qa |grep glibc
   ```


3. 可以看到最高只支持2.12版本，所以考虑编译解决这个问题：

   a. 到<http://www.gnu.org/software/libc/>下载最新版本，我这里下载了[glibc-2.14.tar.xz](http://mirror.bjtu.edu.cn/gnu/libc/glibc-2.14.tar.xz) 这个版本，解压到任意目录准备编译

    b.这里解压到/var/VMdisks/glibc-2.14/

   c.在glibc源码目录建立构建目录，并cd进入构建目录`mkdir build && cd build`

    d.运行configure配置，`make && sudo  make install`

   ```bash
   $ ../configure --prefix=/opt/glibc-2.14
   $ make -j4 
   $ sudo make install
   ```

4. 临时修改环境变量`export LD_LIBRARY_PATH=/opt/glibc-2.14/lib:$LD_LIBRARY_PATH`

5. 此时运行正常，问题解决。



