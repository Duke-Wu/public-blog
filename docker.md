#### docker 常用命令

官方文档：https://docs.docker.com/engine/reference/commandline/docker/

```
# 搜索镜像
docker search tomcat

# 查看镜像
docker images

# 拉取镜像：根据镜像名和tag
docker pull tomcat:8.5.32

# 运行容器
# 如:docker run --name myredis –d redis
docker run --name container-name -d image-name:tag

# 使用bash进入容器
# 如:docker exec  -it  192.168.254.181/el6.9/compile:v18  /bin/bash
docker exec -it 【容器ID】 bash

# 查看运行中的容器
docker ps
docker ps -a

# 停止当前运行的指定容器
docker stop container-name/container-id

# 启动容器
docker start container-name/container-id

# 删除指定容器
docker rm container-id

# 端口映射
-p 6379:6379
如:docker run  --name myredis  -d -p 6379:6379 docker.io/redis	
-p:主机端口映射到容器内部的端口

# 端口文件映射
docker run -itd -p9002:22  --name hello_9002  -v /tmp/init_docker_script/:/tmp/init_docker_script/ 192.168.254.72/el7.5-ssh bash /tmp/init_docker_script/init_docker.sh

# 查看容器日志
docker logs container-name/container-id

# 在容器里执行命令
docker exec 容器名  /it/venv/bin/python /var/www/compile_helper/manage.py migrate

# 重启容器
docker restart 'container_name'

```

## 制作一个属于自己的 docker 容器
1、docker ps 查看正在运行的容器.

2、docker exec –it  3bd0eef03413 bash  进入正在运行的容器内

3、进入容器后，就可以修改镜像了，比如修改镜像中已经部署的代码或者安装新的软件或包等，修改完成之后，exit 退出容器

4、docker commit 3bd0eef03413  demo：v1.3  提交你刚才修改的镜像，新的镜像名称为demo，版本为v1.1


## 创建本地镜像
根据已有容器创建一个本地镜像：commit 命令

docker commit -m "update index.html" --author='leeyunt' 13af96130e40 leeyunt/nginx:v1

命令解释：

参数-m是对创建的该镜像的一个简单描述。
--author表示该镜像的作者。
13af96130e40表示创建镜像所依据的容器的id。
leeyunt/nginx则表示仓库名，leeyunt是名称空间，nginx是镜像名。
v1表示仓库的tag。
创建完成后，通过docker images命令就可以查看到刚刚创建的镜像。
通过刚刚创建的镜像运行一个容器，访问该容器，发现nginx默认的首页已经发生改变。


## docker run 的 -i -t -d 参数
-i    以交互模式运行容器，通常与 -t 同时使用；
-t    为容器重新分配一个虚输入终端，通常与 -i 同时使用；
-d    后台运行容器，并返回容器ID；

常见问题：当后台运行一个镜像 `docker run -d --name server somebody:dockerfile`，使用 `docker ps` 命令查看却发现没有运行成功。
主要原因可能是这个镜像最后添加了 CMD，类似 `CMD: ["/bin/bash"]`。当在后台运行时，容器会首先执行 /bin/bash，shell 会立即退出。
除非在前台运行，否者容器会立即停止。解决方法：通过 -i 或者 -t 提供一个伪 "tty n"。
```
docker run -i -d images:tags
docker run -t -d images:tags
docker run -itd images:tags
```
或者将 tail -f /dev/null 添加到命令中
```
docker run -d centos tail -f /dev/null
```


## docker 运行外部shell命令启动

docker run -i registry-vpc.cn-hangzhou.aliyuncs.com/medlinker/web-monitor-server:test_5299ec04 /bin/sh


## docker cp :用于容器与主机之间的数据拷贝。

语法
```
docker cp [OPTIONS] CONTAINER:SRC_PATH DEST_PATH|-
docker cp [OPTIONS] SRC_PATH|- CONTAINER:DEST_PATH
```
OPTIONS说明：

-L :保持源目标中的链接

实例
将主机/www/runoob目录拷贝到容器96f7f14e99ab的/www目录下。
```bash
docker cp /www/runoob 96f7f14e99ab:/www/
```

将主机/www/runoob目录拷贝到容器96f7f14e99ab中，目录重命名为www。
```bash
docker cp /www/runoob 96f7f14e99ab:/www
```

将容器96f7f14e99ab的/www目录拷贝到主机的/tmp目录中。
```bash
docker cp  96f7f14e99ab:/www /tmp/
```








