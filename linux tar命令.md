---

---




## Linux  tar 命令使用方法


#### 一、tar 简介
>注：tar是打包，不是压缩！
　　解包：`tar xvf FileName.tar`
　　打包：`tar cvf FileName.tar DirName`

常用选项：

- -c, --create                  create a new archive
- -x, --extract, --get       extract files from an archive
- -v, --verbose               verbosely list files processed
- -f, --file=ARCHIVE             use archive file or device ARCHIVE
- -z, --gzip, --gunzip, --ungzip   filter the archive through gzip



#### 二、tar 使用场景
##### 1. 将整个 `/home/www/images` 目录下的文件全部打包为 `/home/www/images.tar`

```bash
tar -cvf /home/www/images.tar /home/www/images    仅打包，不压缩
tar -zcvf /home/www/images.tar.gz /home/www/images    打包后，以gzip压缩
```

> 在参数f后面的压缩文件名是自己取的，习惯上用tar来做，如果加z参数，则以tar.gz 或tgz来代表gzip压缩过的tar file文件



##### 2. 将tgz文件解压到指定目录
`tar zxvf test.tgz -C 指定目录`
比如将 `/source/kernel.tgz`解压到 `/source/linux-2.6.29` 目录

```bash
tar zxvf /source/kernel.tgz -C /source/ linux-2.6.29
```



##### 3. 将指定目录压缩到指定文件
比如将 `linux-2.6.29` 目录压缩到 `kernel.tgz`

```bash
tar czvf kernel.tgz linux-2.6.29
```

 

##### 4. 打包的时候排除某些目录

我们以tomcat 为例，打包的时候我们要排除 `tomcat/logs` 目录，命令如下：
```bash
tar -zcvf tomcat.tar.gz --exclude=tomcat/logs tomcat
```
如果要排除多个目录，增加 --exclude 即可，如下命令排除logs和libs两个目录及文件xiaoshan.txt：
```bash
tar -zcvf tomcat.tar.gz --exclude=tomcat/logs --exclude=tomcat/libs --exclude=tomcat/xiaoshan.txt tomcat
```



#### 三、注意事项：

大家都知道Linux在使用tab键的时候会对目录名称自动补全，这很方便，大家也比较常用。

如我们输入 tomcat/lo 的时候按tab键，命令行会自动生成 tomcat/logs/ ，对于目录，最后会多一个 “/”

这里大家要注意的时候，在我们使用tar 的--exclude 命令排除打包的时候，不能加“/”，否则还是会把logs目录以及其下的文件打包进去。

**~~错误写法：~~**

```
tar -zcvf tomcat.tar.gz --exclude=tomcat/logs/ --exclude=tomcat/libs/ tomcat
```
**正确写法：**

```
tar -zcvf tomcat.tar.gz --exclude=tomcat/logs --exclude=tomcat/libs tomcat
```



#### 四、tar 常见压缩格式

注意：

tar命令打包还是压缩需要看所调用的命令参数....

tar在使用时可以调用命令参数,比如tar -xvf  +文件名就是解包,但是不是解压...

只有在使用了参数z等调用gzip等压缩命令时才是压缩或者解压.

比如将整个 /etc 目录下的文件全部打包成为etc.tar 

```
tar -cvf h5.tar.gz h5    <==仅打包，不压缩！ 
tar -zcf h5.tar.gz h5    <==打包后，以 gzip 压缩
tar -jcvf h5.tar.bz2 h5    <==打包后，以 bzip2 压缩
tar zcf newzhibo.tar.gz --exclude=newzhibo.tra.gz newzhibo  <==压缩 newzhibo 文件夹时排除文件中的 newzhibo.tra.gz 文件。
```



---

　　.gz

　　解压1：gunzip FileName.gz

　　解压2：gzip -d FileName.gz

　　压缩：gzip FileName  (压缩只能对于文件，如果要压缩文件夹，请用tar先打包)

　　.tar.gz 和 .tgz

　　解压：tar zxvf FileName.tar.gz

　　压缩：tar zcvf FileName.tar.gz DirName

　　———————————————



　　.bz2 

　　解压1：bzip2 -d FileName.bz2 

　　解压2：bunzip2 FileName.bz2 

　　压缩： bzip2 -z FileName 

　　.tar.bz2 

　　解压：tar jxvf FileName.tar.bz2 或 tar --bzip -xvf FileName.tar.bz2 

　　压缩：tar jcvf FileName.tar.bz2 DirName 

　　———————————————



　　.bz 

　　解压1：bzip2 -d FileName.bz 

　　解压2：bunzip2 FileName.bz 

　　压缩：未知 

　　.tar.bz 

　　解压：tar jxvf FileName.tar.bz 

　　压缩：未知 

　　———————————————



　　.Z 

　　解压：uncompress FileName.Z 

　　压缩：compress FileName 

　　.tar.Z 

　　解压：tar Zxvf FileName.tar.Z 

　　压缩：tar Zcvf FileName.tar.Z DirName

　　———————————————

 

　　.zip 

　　解压：unzip FileName.zip 

　　压缩：zip FileName.zip DirName 

　　压缩一个目录使用 -r 参数，-r 递归。例： $ zip -r FileName.zip DirName

　　———————————————



　　.rar 

　　解压：rar x FileName.rar 

　　压缩：rar a FileName.rar DirName 

　　rar 请到：http://www.rarsoft.com/download.htm 下载！ 

　　解压后请将rar_static拷贝到/usr /bin目录（其他由$PATH环境变量指定的目录也可以）： 

　　[root@www2 tmp]# cp rar_static /usr/bin/rar 

　　———————————————

 

　　.lha 

　　解压：lha -e FileName.lha 

　　压缩：lha -a FileName.lha FileName  

　　lha请到：http://www.infor.kanazawa-it.ac.jp/~ishii/lhaunix/下载！ 

　　解压后请将 lha拷贝到/usr/bin目录（其他由$PATH环境变量指定的目录也可以）： 

　　[root@www2 tmp]# cp lha /usr/bin/

　　——————————————— 



　　.rpm 

　　解包：rpm2cpio FileName.rpm | cpio -div 

　　——————————————— 



　　.deb 

　　解包：ar p FileName.deb data.tar.gz | tar zxf - 

　　——————————————— 



　　.tar .tgz .tar.gz .tar.Z .tar.bz .tar.bz2 .zip .cpio .rpm .deb .slp .arj .rar .ace .lha .lzh .lzx .lzs .arc .sda .sfx .lnx .zoo .cab .kar .cpt .pit .sit .sea 

　　解压：sEx x FileName.* 

　　压缩：sEx a FileName.* FileName 

　　 

　　sEx只是调用相关程序，本身并无压缩、解压功能，请注意！ 

　　sEx请到： http://sourceforge.net/projects/sex下载！ 

　　解压后请将sEx拷贝到/usr/bin目录（其他由$PATH环境变量指定的目录也可以）： 

　　[root@www2 tmp]# cp sEx /usr/bin/