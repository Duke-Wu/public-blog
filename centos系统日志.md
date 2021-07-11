# Centos系统日志

## /etc/logrotate.conf

/etc/logrotate.conf 是 Logrotate 工具的一个配置文件，这个工具用来自动切割系统日志，Logrotate 是基于 cron 来运行的，如下：


```bash
[root@localhost ~]$ cat /etc/cron.daily/logrotate    # 每天运行
#!/bin/sh

/usr/sbin/logrotate /etc/logrotate.conf >/dev/null 2>&1
EXITVALUE=$?
if [ $EXITVALUE != 0 ]; then
    /usr/bin/logger -t logrotate "ALERT exited abnormally with [$EXITVALUE]"
fi
exit 0
```



实际运行时，Logrotate 会调用配置文件 /etc/logrotate.conf ，默认的配置如下：


```bash
[root@localhost ~]$ cat /etc/logrotate.conf 

weekly                      # 每周切割一次
rotate 4                    # 只保留四份文件
create                      # 切割后会创建一个新的文件
dateext                     # 指定切割文件的后缀名，这里以日期为后缀名
include /etc/logrotate.d    # 包含其他配置文件的目录

/var/log/wtmp {             # 对哪个文件进行切割
    monthly                 # 每个月切割一次
    create 0664 root utmp   # 指定创建的新文件的权限，属主，属组
        minsize 1M          # 文件容量超过这个值时才进行切割
    rotate 1                # 只保留一份文件
}

/var/log/btmp {
    missingok
    monthly
    create 0600 root utmp
    rotate 1
}
```



## /var/log/secure.log

Checking successful and failed login attempts using grep command
```bash
grep "Accepted password" /var/log/secure | more
```
Use the following format to print only Failed attempts:

```bash
grep "Failed password" /var/log/secure | more
```





[12 Critical Linux Log Files You Must be Monitoring](https://www.eurovps.com/blog/important-linux-log-files-you-must-be-monitoring/)

[How to track successful and failed login attempts in Linux](https://www.2daygeek.com/check-track-successful-failed-login-attempts-linux/)
