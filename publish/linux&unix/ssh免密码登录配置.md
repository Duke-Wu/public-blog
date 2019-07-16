---
title: ssh免密码登录配置
date: 2019/1/1 0:0:0
updated: 2019/7/4 14:40:25
comments: true
tags:
- linux
categories:
- tool kits
---


## ssh免密码登录配置

## 1. 创建sshkey

```bash
ssh-keygen -t rsa
```

![](http://img-note.wuqianlin.cn/img-md/2018-11-23-014156.jpg)

> /home/hadoop目录下会产生一个.ssh文件夹，.ssh文件夹内有私钥文件 id_rsa和 公钥文件 id_rsa.pub。



## 2. 使用ssh-copy-id 命令把自己的公钥发送给需要免密登录的主机

命令如下：

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub hadoop@192.168.8.94
```

其中，hadoop为对方的要执行ssh链接的用户名，192.168.8.94是对方主机的ip地址，我在hosts中做了配置，也可以用配置后的别名，如下图：

![](http://img-note.wuqianlin.cn/img-md/2018-11-23-014831.jpg)

> 中间输入的密码是对方主机用户的密码。



## 3. 直接免密登录到对方主机

```bash
ssh hadoop@192.168.8.94
```

如果两个主机用户名相同，那么可以直接通过 ssh 对方ip