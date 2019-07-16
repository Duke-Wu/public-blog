---
title: 每天一个Linux命令-curl
date: 2019/1/1 0:0:0
updated: 2019/7/4 14:40:25
comments: true
tags:
- linux
categories:
- tool kits
---

> curl命令是一个利用URL规则在命令行下工作的文件传输工具。它支持文件的上传和下载。curl支持包括HTTP、HTTPS、ftp等众多协议，还支持POST、cookies、认证、从指定偏移处下载部分文件、用户代理字符串、限速、文件大小、进度条等特征。

# 每天一个Linux命令-curl

#### 1. get请求

使用curl发送GET请求：curl protocol://address:port/url?args

```bash
curl http://127.0.0.1:8080/login?admin&passwd=12345678
```

如果想看到详细的请求信息，我们可以加上 `-v` 参数

```bash
curl http://127.0.0.1:8080/login?admin&passwd=12345678 -v
```



#### 2. post请求

使用curl发送POST请求：

```bash
# curl -d "args" protocol://address:port/url
curl -d "user=admin&passwd=12345678" http://127.0.0.1:8080/login
```

这种方法是参数直接在header里面的，如需将输出指定到文件可以通过重定向进行操作.



#### 3. json格式的post请求

```bash
# curl -H "Content-Type:application/json" -X POST -d 'json data' URL
curl -H "Content-Type:application/json" -X POST -d '{"user": "admin", "passwd":"12345678"}' http://127.0.0.1:8000/login
```

> ps：json串内层参数需要格式化



#### 4. POST 上传文件

用 `-F "file=@__FILE_PATH__"` 的请示，传输文件即可。命令如下：

```bash
curl -H "token: 222" localhost:8000/api/v1/upimg -F "file=@/Users/fungleo/Downloads/401.png"  -v
```



#### 5. 常见参数

| 选项                    | 解释                     |
| :---------------------- | :----------------------- |
| -X/--request <[command] | 指定什么命令             |
| -H/--header <line>      | 自定义头信息传递给服务器 |
| -d/--data <data>        | HTTP POST方式传送数据    |
| -v                      | 查看详情                 |
| -i/--include            | 输出时包括protocol头信息 |
| -I/--[head]             | 只显示请求头信息         |

关于curl的更多信息可以查看[http://man.linuxde.net/curl](http://man.linuxde.net/curl)