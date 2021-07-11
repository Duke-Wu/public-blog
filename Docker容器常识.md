## 容器分为交互式容器和守护进程式容器

### 交互式容器

```
# -it 交互式的容器，一般做为工具类的容器，退出即exit  
# 加--rm参数，容器退出时自动删除
# 创建直接进入容器
docker run -it --name containername imagename:tag
```



### 守护进程式容器

```
docker run -d --name test2 nginx:1.14
```

docker run 默认command参数是/bin/bash

docker run的command参数优先级高于image的CMD优先级



## Docker 客户端连接 Docker Daemon 的方式

Docker为C/S架构，服务端为docker daemon，客户端为docker.service，支持**本地unix socket域套接字通信与远程socket通信**。

**默认为本地unix socket通信**，要支持**远程客户端访问**需要做如下设置（不安全，仅用于测试），

1、UNIX域套接字

默认就是这种方式，会生成一个/var/run/docker.sock文件，UNIX域套接字用于本地进程之间的通讯, 这种方式相比于网络套接字效率更高，但局限性就是只能被本地的客户端访问。

 2、TCP端口监听

服务端开启端口监听dockerd -H | --host IP:PORT , 客户端通过指定IP和端口访问服务端 docker -H IP:PORT

通过这种方式，**任何人只要知道暴露的****ip****和端口就能随意访问****docker****服务****(**因为docker的权限很高，一旦被突破就能够取得服务端宿主机的最高权限)，因此这种方式一定要使用TLS加密通信

 3、启动docker守护进程时可以同时监听多个socket

 Docker 客户端要和 Docker daemon 通过 REST API 通信，那就让我们看看它们可以采用的方法有哪些：

1. Unix socket (-H unix:///var/run/docker.sock)
2. Systemd socket activation (-H fd://)
3. Tcp (-H tcp://127.0.0.1:2376)

1和2可理解成一种方式，就是同一台主机上的进程间通信。

dockerd -H unix:///var/run/docker.sock -H tcp://127.0.0.1:2376 -H tcp://127.0.0.1:2377

**-H fd://方式不能与-H tcp://方式同时使用，若同时使用仅前者生效**

若要socket和tcp方式同时使用，则使用unix:///和tcp://

