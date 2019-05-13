

### pip离线安装python模块

1. 下载指定的包到指定文件夹。
  ```bash
  pip  list    # 查看安装的包
  pip  freeze  >  requirements.txt    # 将pip安装的包名记录到 requirements.txt 文件中
  mkdir packs    # 创建存放安装包的目录
  pip  download  -d  packs  six    # 下载pandas包
  或
  pip download  -d  packs  -r  requirements.txt    # 下载 requirements.txt 列出的所有包
  ```
  
  

2. 安装指定的离线包
  ```bash
  pip install  --no-index  --find-links=packs  six
  或   
  pip install  --no-index  --find-links=packs  -r  requirements.txt
  ```



### 参考

[pip离线安装](https://www.cnblogs.com/wt11/p/6216508.html)