---
title: Python 1秒钟启动一个下载服务器
date: 2019/1/1 0:0:0
updated: 2019/7/4 14:40:25
comments: true
tags:
- python
categories:
- tool kits
---

## Python 1秒钟启动一个下载服务器

进入一个目录，然后执行

```bash
python -m SimpleHTTPServer # Pyhon2
python -m http.server      # Python3
```

然后就可以用http://ip:8000访问这个目录的内容了