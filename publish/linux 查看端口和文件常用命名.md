# linux 查看端口和文件常用命名

## 1、lsof

Lists on its standard output file information about files opened by processe.

Example:

```bash
# lsof -i:22
COMMAND   PID USER   FD   TYPE  DEVICE SIZE/OFF NODE NAME
sshd     1306 root    3u  IPv4   15087      0t0  TCP *:ssh (LISTEN)
```

> lsof 初级教程：https://www.jianshu.com/p/a3aa6b01b2e1

<!-- more -->


## 2、netstat

Print  network  connections, routing tables, interface statistics, masquerade connections, and multicast memberships

Example:

```bash
# netstat -atnp
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1306/sshd           
tcp        0      0 172.27.0.4:58936        169.254.0.55:5574       ESTABLISHED 565/YDService       
tcp        0     96 172.27.0.4:22           110.184.64.129:17796    ESTABLISHED 13646/sshd: root@pt 
```

参数的含义:

```
-t (tcp) 仅显示tcp相关选项
-u (udp) 仅显示udp相关选项
-n 拒绝显示别名，能显示数字的全部转化为数字
-l 仅列出在Listen(监听)的服务状态
-p 显示建立相关链接的程序名
```



