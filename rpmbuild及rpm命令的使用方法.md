# rpmbuild及rpm命令的使用方法

## 一、rpmbuild

## 1. spec 文件模块调用方法顺序

rpmbuild 按顺序调用 %prep  %build  %install

rpm -e 按顺序调用 %preun %postnun

rpm -ivh 按顺序调用 %pretrans  %pre  %post  %posttrans

 rpm -U 按顺序调用 %pretrans  %pre  %post  %preun  %postun  %posttrans


## 2. spec文件中判断是升级or卸载

我们在写spec文件时，有pre、post、preun、postun等模块用于指示安装前后以及卸载前后要执行的动作，而软件升级实际上是先卸载后安装的过程，所以这些模块内动作的编写需要区分是卸载还是升级以便采取不同的动作。

那么如何来进行区分呢？

脚本片段传递一个参数，用于表示本软件包的个数。执行特定动作时，通过向 `$1`传递不同值，来表示不同动作（安装/升级/卸载），除了 %pretrans 和 %posttrans 它们的 `$1` 为 0 (rpm 4.4+ 支持 %pretrans 和 %posttrans)。对于安装、升级和卸载，所传递的参数值如下表所示：

| 项目           | 安装（install）                                              | 升级（update/upgrade）                                       | 卸载（remove/erase）                                         |
| -------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| %pre           | `$1`==1                                                      | `$1` == 2                                                    | (N/A)                                                        |
| %post          | `$1` == 1                                                    | `$1` == 2                                                    | (N/A)                                                        |
| %preun         | (N/A)                                                        | `$1` == 1                                                    | `$1` == 0                                                    |
| %postun        | (N/A)                                                        | `$1` == 1                                                    | `$1` == 0                                                    |
| %pretrans      | `$1` == 0                                                    | `$1` == 0                                                    | (N/A)                                                        |
| %triggerprein  | 安装本包: `$1` == 0, `$2` == 1 安装目标包: `$1` == 1, `$2` == 0 | `$1` == 1, `$2` == 1                                         | (N/A)                                                        |
| %triggerin     | `$1` == 1, `$2` == 1                                         | 升级本包: `$1` == 2, `$2` == 1升级目标包: `$1` == 1, `$2` == 2 | (N/A)                                                        |
| %triggerun     | (N/A)                                                        | `$1` == 1, `$2` == 1                                         | 卸载本包: `$1` == 0, `$2` == 1卸载目标包: `$1` == 1, `$2` == 0 |
| %triggerpostun | (N/A)                                                        | 升级目标包: `$1` == 1, `$2` == 1                             | 卸载目标包: `$1` == 1, `$2` == 0                             |
| %posttrans     | `$1` == 0                                                    | `$1` == 0                                                    | (N/A)                                                        |

注意，如果安装相同软件包的多个版本，这些参数值将会不同（这发生于同时安装包，如 kernel 和 multilib 包。然而，它会引发错误，防止软件包升级完成）。所以，使用以下结构的脚本是个好主意：

```
%pre
if [ $1 -gt 1 ] ; then   # -gt大于
fi
```

## 3. 参考

http://ftp.rpm.org/max-rpm/index.html

https://www.cnblogs.com/michael-xiang/p/10480809.html

https://blog.csdn.net/mrbuffoon/article/details/81105169



## 二、rpm常用重要命令

rpm -qpl name-version-noarch.rpm    # 列出rpm包的内容

rpm2cpio name-version-noarch.rpm | cpio -div    # 解压rpm包内容

rpm -Uvh name-version-noarch.rpm    # 升级一个包

rpm -ivh name-version-noarch.rpm    # 安装一个包

rpm -e name-version-noarch.rpm    # 卸载一个包

rpm -q name-version-noarch    # 查询一个包是否被安装

rpm -qf    # 列出服务器上的一个文件属于哪一个RPM包

rpm -qa    # 列出所有被安装的rpm package 

> 安装参数

--force     # 即使覆盖属于其它包的文件也强制安装

--nodeps    # 即使该rpm包依赖不满足也强制安装


### 1. rpm安装

命令格式： rpm -i ( or --install) options file1.rpm ... fileN.rpm 

参数： file1.rpm ... fileN.rpm 将要安装的RPM包的文件名 

详细选项： 

-h (or --hash) 安装时输出hash记号 (``#'') 
--test 只对安装进行测试，并不实际安装。 
--percent 以百分比的形式输出安装的进度。 
--excludedocs 不安装软件包中的文档文件 
--includedocs 安装文档 
--replacepkgs 强制重新安装已经安装的软件包 
--replacefiles 替换属于其它软件包的文件 
--force 忽略软件包及文件的冲突 
--noscripts 不运行预安装和后安装脚本 
--prefix 将软件包安装到由 指定的路径下 
--ignorearch 不校验软件包的结构 
--ignoreos 不检查软件包运行的操作系统 
--nodeps 不检查依赖性关系 
--ftpproxy 用 作为 FTP代理 
--ftpport 指定FTP的端口号为 

通用选项 

-v 显示附加信息 
-vv 显示调试信息 
--root 让RPM将指定的路径做为"根目录"，这样预安装程序和后安装程序都会安装到这个目录下 
--rcfile 设置rpmrc文件为 
--dbpath 设置RPM 资料库存所在的路径为 

### 2. rpm卸载 

命令格式： rpm -e ( or --erase) options pkg1 ... pkgN 

参数：pkg1 ... pkgN ：要删除的软件包 

详细选项 

--test 只执行删除的测试 
--noscripts 不运行预安装和后安装脚本程序 
--nodeps 不检查依赖性 

通用选项 

-vv 显示调试信息 
--root 让RPM将指定的路径做为"根目录"，这样预安装程序和后安装程序都会安装到这个目录下 
--rcfile 设置rpmrc文件为 
--dbpath 设置RPM 资料库存所在的路径为 

### 3. rpm升级 

命令格式：rpm -U ( or --upgrade) options file1.rpm ... fileN.rpm 

参数：file1.rpm ... fileN.rpm 软件包的名字 

详细选项 

-h (or --hash) 安装时输出hash记号 (``#'') 
--oldpackage 允许"升级"到一个老版本 
--test 只进行升级测试 
--excludedocs 不安装软件包中的文档文件 
--includedocs 安装文档 
--replacepkgs 强制重新安装已经安装的软件包 
--replacefiles 替换属于其它软件包的文件 
--force 忽略软件包及文件的冲突 
--percent 以百分比的形式输出安装的进度。 
--noscripts 不运行预安装和后安装脚本 
--prefix 将软件包安装到由 指定的路径下 
--ignorearch 不校验软件包的结构 
--ignoreos 不检查软件包运行的操作系统 
--nodeps 不检查依赖性关系 
--ftpproxy 用 作为 FTP代理 
--ftpport 指定FTP的端口号为 

通用选项 

-v 显示附加信息 
-vv 显示调试信息 
--root 让RPM将指定的路径做为"根目录"，这样预安装程序和后安装程序都会安装到这个目录下 
--rcfile 设置rpmrc文件为 
--dbpath 设置RPM 资料库存所在的路径为 

### 4. rpm查询 

命令格式：rpm -q ( or --query) options 

参数：pkg1 ... pkgN ：查询已安装的软件包 

详细选项 

-p (or ``-'') 查询软件包的文件 
-f 查询属于哪个软件包 
-a 查询所有安装的软件包 
--whatprovides 查询提供了 功能的软件包 
-g 查询属于 组的软件包 
--whatrequires 查询所有需要 功能的软件包 

信息选项 

显示软件包的全部标识 
-i 显示软件包的概要信息 
-l 显示软件包中的文件列表 
-c 显示配置文件列表 
-d 显示文档文件列表 
-s 显示软件包中文件列表并显示每个文件的状态 
--scripts 显示安装、卸载、校验脚本 
--queryformat (or --qf) 以用户指定的方式显示查询信息 
--dump 显示每个文件的所有已校验信息 
--provides 显示软件包提供的功能 
--requires (or -R) 显示软件包所需的功能 

通用选项 

-v 显示附加信息 
-vv 显示调试信息 
--root 让RPM将指定的路径做为"根目录"，这样预安装程序和后安装程序都会安装到这个目录下 
--rcfile 设置rpmrc文件为 
--dbpath 设置RPM 资料库存所在的路径为 

### 5. 校验已安装的软件包 

命令格式：rpm -V ( or --verify, or -y) options 

参数：pkg1 ... pkgN 将要校验的软件包名 

软件包选项 

-p Verify against package file 
-f 校验所属的软件包 
-a Verify 校验所有的软件包 
-g 校验所有属于组 的软件包 

详细选项 

--noscripts 不运行校验脚本 
--nodeps 不校验依赖性 
--nofiles 不校验文件属性 

通用选项 

-v 显示附加信息 
-vv 显示调试信息 
--root 让RPM将指定的路径做为"根目录"，这样预安装程序和后安装程序都会安装到这个目录下 
--rcfile 设置rpmrc文件为 
--dbpath 设置RPM 资料库存所在的路径为 

### 6. 校验软件包中的文件 

语法： rpm -K ( or --checksig) options file1.rpm ... fileN.rpm 

参数：file1.rpm ... fileN.rpm 软件包的文件名 

Checksig--详细选项 

--nopgp 不校验PGP签名 

通用选项 

-v 显示附加信息 
-vv 显示调试信息 
--rcfile 设置rpmrc文件为 


### 7. 其它RPM选项 

--rebuilddb 重建RPM资料库 
--initdb 创建一个新的RPM资料库 
--quiet 尽可能的减少输出 
--help 显示帮助文件 
--version 显示RPM的当前版本



[RPM 包的构建 - SPEC 基础知识](https://www.cnblogs.com/michael-xiang/p/10480809.html)

[RPM命令详解（安装、升级、卸载）](https://www.cnblogs.com/dadonggg/p/8191390.html)