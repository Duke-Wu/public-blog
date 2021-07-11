#### EXPOSE： 暴露容器的端口

```
docker run --help | grep publish
-p, --publish list                   Publish a container's port(s) to the host
-P, --publish-all                    Publish all exposed ports to random ports
# -p 将容器的端口映射到宿主机上
# -P 结合Dockerfile文件，将容器的EXPOSE端口随机映射到宿主机上 
```

`EXPOSE` 在 `dockerfile` 中暴露的容器端口，在使用 `docker run -d --net=host  image:tag`  命令启动时直接映射到宿主机同样的端口，如果该端口被占用则会报错。 

EXPOSE指令是声明运行时容器服务端口，这只是一个声明，在运行时并不会因为这个声明应用就会开启这个端口的服务。在Dockerfile中这样声明有两个好处：一个是帮助镜像使用者更好的理解这个镜像服务的守护端口，另一个作用则是在运行时使用随机端口映射时，也就是docker run -p命令时，会自动随机映射EXPOSE端口。

要将EXPOSE和在运行时使用-p <宿主>:<容器端口>区分开来，-p是映射宿主端口和容器端口，换句话说，就是将容器的对应端口服务公开给外界访问，而EXPOSE仅仅是声明端口使用什么端口而已，并不会自动在宿主进行端口映射。




#### ENV指令

ENV指令用于在构建镜像过程中设置环境变量，后续的RUN可以使用它所创建的环境变量。当该镜像运行时，会自动设置ENV所设置的环境变量。

指令格式如下：

```
ENV <key>=<value> ...
```

引号和反斜杠可以用于在值中包含空格。

```bash
ENV MY_NAME="John Doe"
ENV MY_DOG=Rex\ The\ Dog
ENV MY_CAT=fluffy
```

`ENV` 指令允许多个 `<key>=<value> ...` 变量同时设置，下面的例子将在生成的镜像中产生相同的结果：

```dockerfile
ENV MY_NAME="John Doe" MY_DOG=Rex\ The\ Dog \
    MY_CAT=fluffy
```

当使用生成的镜像运行容器时，使用 `ENV` 设置的环境变量将持久存在于容器内。

你可以使用 `docker inspect` 查看这些值，并使用 `docker run --env <key>=<value>` 修改它们。

环境变量持久性可能会导致意想不到的副作用。
 例如，设置 `ENV DEBIAN_FRONTEND=noninteractive` 会改变 `apt-get` 的行为，并可能让使用镜像的用户感到困惑。

如果只在构建过程中需要环境变量，而不是在最终镜像中，请考虑为单个命令设置一个值：

```dockerfile
RUN DEBIAN_FRONTEND=noninteractive apt-get update && apt-get install -y ...
```

或者使用 `ARG`，它不会在最终镜像中持久存在：

```dockerfile
ARG DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y ...
```

> 替代语法
>
> `ENV` 指令还允许另一种语法 `ENV <key> <value>`，省略了中间的等号。例如：
>
> ```dockerfile
> ENV MY_VAR my-value
> ```
>
> 这种语法不允许在一条 `ENV` 指令中设置多个环境变量，可能会造成混淆。例如，下面的代码设置了一个值为“TWO= THREE=world”的环境变量（ONE）：
>
> ```dockerfile
> ENV ONE TWO= THREE=world
> ```
>
> 支持这种替代语法为了向后兼容，但由于上述原因不鼓励使用，可能会在将来的版本中删除。

在ENV指令中，可以直接引用如下环境变量：

```
HOME: 用户家目录
HOSTNAME: 容器默认主机名
PATH: 
TERM: 默认xterm
```

在docker run 命令中通过 -e标记来传递环境变量，在容器运行时就可以使用该变量。

```bash
docker run -it -e "evn=new" image:tag /bin/bash
```

注意：ENV定义的环境变量不能被CMD指令使用，也不能被 docker run 的命令参数引用。

语法参考：https://www.jianshu.com/p/6b6c4d44da03



#### ARG

语法：`ARG <name>[=<default value>]`

构建参数ARG和ENV指令一样，都是设置环境变量。与之不同的是，ARG设置的环境变量只是在镜像构建时所设置的，在将来容器运行时是不会存在这些环境变量的。但是不要因此就用ARG来保存密码之类的信息，因为通过docker history还是能够看得到的。ARG指令与ENV指令的使用类似，如下：

```
FROM ubuntu:16.04
ARG app="python-pip"
RUN apt-get update && apt-get install -y $app && rm -rf /var/lib/apt/lists/*
```

设置变量命令，ARG命令定义了一个变量，在docker build创建镜像的时候，使用 --build-arg `<varname>=<value>`来指定参数

如果用户在build镜像时指定了一个参数没有定义在Dockerfile种，那么将有一个Warning

提示如下：

```
[Warning] One or more build-args [foo] were not consumed.
```

我们可以定义一个或多个参数，如下：

```
FROM busybox
ARG user1
ARG buildno
...
```

也可以给参数一个默认值：

```
FROM busybox
ARG user1=someuser
ARG buildno=1
...
```

如果我们给了ARG定义的参数默认值，那么当build镜像时没有指定参数值，将会使用这个默认值



#### COPY 与 ADD 指令

https://www.cnblogs.com/sparkdev/p/9573248.html

Build 上下文的概念

在通过 Dockerfile 创建镜像时，会产生一个 build 上下文(context)。这个上下文就是 docker build 命令的PATH或URL指定路径的文件集合。在镜像 build 过程中可以引用上下文中的任何文件。例如 COPY 和 ADD 命令。

默认情况下`docker build -t imagename:tag .`命令中的“**.**”表示build上下文为当前目录。也可以指定一个目录为上下文，例如：

```
docker build -t imagename:tag /home/nick/hc
```

以上命令指定 /home/nick/hc 目录为 build 上下文，默认情况下 docker 使用在上下文的根目录下找到的 Dockerfile 文件。

注意COPY与ADD命令不能拷贝上下文之外的本地文件。

对于COPY和ADD命令来讲，如果要把本地文件拷贝到镜像当中，那么此文件必须存在于上下文目录中。此限制很好理解，因为在执行build命令时，docker 客户端会把上下文中的所有文件发送给 docker daemon。考虑到docker客户端和docker daemon不在同一台服务器上，build命令只能从上下文中获取文件。

**COPY命令的简单使用**

```
COPY <src> <dest>
```

原路径支持go语言风格的通配符：

```
COPY check* /testdir/           # 拷贝所有 check 开头的文件
COPY check?.log /testdir/       # ? 是单个字符的占位符，比如匹配文件 check1.log
```

对于目录而言，COPY 和 ADD 命令具有相同的特点：**只复制目录中的内容而不包含目录自身。**

如果需要复制目录，需要在目标路径中指定这个目录的名称：

```
# nickdir 为目录
WORKDIR  /app
COPY nickdir  ./nickdir
```

目标路径是容器内的绝对路径，也可以是工作目录下的相对路径，工作目录可以使用WORKDIR指令进行指定。**目标路径不需要事先创建，Docker会自动创建所需的文件目录。**使用COPY指令会将源路径的文件的所有元数据，比如读、写、指定权限、时间变更等。如果源路径时一个目录，那么会将整个目录复制到容器中，包括文件系统元数据。

在 multistage 的用法中，可以使用 COPY 命令把前一阶段构建的产物拷贝到另一个镜像中。



**ADD命令简单使用**

```
ADD <src> <dest>
```

除了不能用于 multistage 场景下，ADD 可以完成 COPY 命令所有功能，并且可以完成两类特殊的功能：

- 解压压缩文件并把它们添加到镜像中
- 从 url 拷贝文件到镜像中

`ADD`指令和`COPY`的格式和性质基本一致，只不过是在`COPY`的基础上增加了一些功能。例如`ADD`指定中，源路径可以是一个远程URL，Docker引擎会自动帮我们将远程URL的文件下载下来到目标路径下，例如：

```
ADD http://192.168.0.89:5000/test.py /test/
```

压缩格式为gzip、bzip2以及xz的情况下，ADD指令都会将其解压缩！

非常值得注意的是，目标路径为一个URL时，会将其自动下载到目标路径下，**但是其权限被自动设置成了600**，如果这并不是你想要的权限，那么你还需要额外增加一层RUN命令进行更改，另外，如果下载的是一个压缩包，同样你还需要额外增加一层RUN命令进行解压缩。所以，在这种情况下，你还不如指定只用一层RUN，使用curl或者wget工具进行下载，并更改权限，然后进行解压缩，最后清理无用文件！

当你的源路径为压缩文件并且不想让Docker引擎将其自动解压缩，这个时候就不可以使用ADD命令，你可以使用COPY命令进行完成！

其实ADD命令并不实用，并不推荐使用！！！



#### WORKDIR

语法：`WORKDIR /path/to/workdir`

设置工作目录，对RUN,CMD,ENTRYPOINT,COPY,ADD生效。

 WORKDIR也可以解析环境变量，如：

```
ENV DIRPATH /path
WORKDIR $DIRPATH/$DIRNAME
RUN pwd
```

pwd的执行结果是/path/$DIRNAME

使用WORKDIR指令来制定工作目录(或者称为当前目录)，以后各层操作的当前目录就是为指定的目录，**如果该目录不存在，WORKDIR会自动帮你创建目录**，如下：

```
FROM ubuntu:16.04
WORKDIR /data/test
RUN mkdir docker && echo "test" > demo.txt
```

当我们使用docker build构建此镜像，并使用docker run命令进行创建和启动容器之后，会发现目录被自动切换到了/data/test，并且在当前目录下有一个文件夹docker，在docker下有一个文件domo.txt并且有相应的内容。我们还可以为特定的指令指定不同的工作目录，如下：

```
FROM ubuntu:16.04
WORKDIR /data/test
RUN mkdir docker
WORKDIR /data/test/docker
RUN echo "test" > demo.txt
```


这样，Dockerfile中两次RUN指令的操作都在不同的目录下进行，最终容器会切换到最后一次WORKDIR指令下的目录。

WORKDIR指令可以通过docker run命令中的-w参数来进行覆盖。



#### FROM

 功能为指定基础镜像，并且必须是第一条指令。

如果不以任何镜像为基础，那么写法为：FROM scratch。

同时意味着接下来所写的指令将作为镜像的第一层开始

语法：

```
FROM <image>
FROM <image>:<tag>
FROM <image>:<digest> 
```

三种写法，其中`<tag>`和`<digest>` 是可选项，如果没有选择，那么默认值为latest

除了指定现有的基础镜像以外，DockerFile还存在一个特殊的镜像`srcatch`，这个镜像是一个虚拟的概念，并不实际存在，它表示一个空白的镜像：

如果你以scratch作为基础镜像，意味着你将不使用任何镜像为基础，接下来你所写的指令将作为第一层开始存在。不以任何系统为基础，直接将可执行文件复制进镜像的做法并不罕见。如swarm、coreos/etcd。对Linux下静态编译的程序来说，并不需要其他操作提供其运行时支持，所需的一切库都在可执行文件里了，因此使用scratch作为基础，可以使镜像的体积更加小巧。



#### RUN

功能为运行指定的命令

RUN命令有两种格式

```
1. RUN <command>
2. RUN ["executable", "param1", "param2"]
```

第一种后边直接跟shell命令

- 在linux操作系统上默认 /bin/sh -c
- 在windows操作系统上默认 cmd /S /C

第二种是类似于函数调用。

可将executable理解成为可执行文件，后面就是两个参数。

两种写法比对：

- ```
    RUN /bin/bash -c 'source $HOME/.bashrc; echo $HOME
    ```

- ```
    RUN ["/bin/bash", "-c", "echo hello"]
    ```

注意：**DockerFile的每一个指令都会新构建一层**，`RUN`命令也不例外。每一个`RUN`行为，都会新建立一层，然后在其上执行命令，执行完毕后，提交这一层的修改，构成新的镜像！

UnionFS是有最大层数限制的，比如AUFS，曾经是最大不能超过42层，现在是最大不能超过127层。所以，对于一些软件的编译、安装、更新等，分成多层操作，会使得镜像变臃肿，拥有非常多的层，不仅增加了构建部署时间，也容易出错！

可以使用`&&`符号将多个命令分开，使其先后执行。此时，一个`RUN`指令有可能会变得非常长，为了使DockerFile的可阅读性和代码更加美观，我们可以使用`\`进行换行操作。另外，我们还可以使用`#`进行行首的注释。

可以在指令的结尾处添加了清理工作的命令，删除了为了编译构建的软件，清理了所有下载、展开的文件，清理apt缓存文件。镜像是多层存储，每一层存储的东西不会在下一层删除，会一直跟随着镜像。因此在镜像构建时，一定要确保每一层只添加真正需要的东西，任何无关的东西都应该被清理掉。



#### CMD
CMD指令与RUN指令相似，具有两种格式：

```
shell格式：CMD <命令>
exec格式：CMD [“可执行文件”, “参数1”, “参数2”, …]
```

之前介绍容器的时候就说过，Docker不是虚拟机，容器就是进程。既然是进程，那么在启动容器的时候，就需要指定运行的程序及参数。CMD就是指定默认的容器主进程的启动命令的。
在运行时可以设置CMD指令来代替镜像设置中的命令，例如Ubuntu默认的CMD是/bin/bash，当我们使用命令docker run -it ubuntu创建并启动一个容器会直接进入bash。我们也可以在运行时指定运行别的命令，比如docker run -it ubuntu cat /etc/os-release，这就用cat /etc/os-release命令代替了默认的/bin/bash命令，输出了系统版本信息。比如，我想在启动容器的时候，在控制台中输出Hello Docker!，我们可以在Dockerfile中这样写，如下：

```
FROM ubuntu
CMD echo "Hello Docker!"
```

接下来，我们构建一个镜像ubuntu:v1.0，接下来，我们以此镜像为基础创建并启动一个容器，如下：

```
docker run -it ubuntu:v1.0
```


这样，就会在控制台中输出Hello Docker!的信息。

值得注意的是，如果使用shell格式，那么实际的命令会被包装成为sh -c的参数的形式进行执行。上面的CMD指令，在实际执行中会变成：

```
CMD ["sh", "-c", "echo", "Hello Docker!"]
```

因为这种特性，一些命令在加上sh -c之后，有可能会发生意想不到的错误，因此在Dockerfile中使用RUN指令时，更加推荐使用exec格式！最后需要牢记，使用docker run命令指定要执行的命令可以覆盖RUN指令，如果我们的docker run中指定了我们将要执行的命令，并且在Dockerfile中也指定了CMD命令，那么最终只会执行docker run命令中指定的命令。比如有这样一个Dockerfile：

```
FROM ubuntu
CMD ["echo", "Hello Docker!"]
```


我们将其构建成成镜像ubuntu:v1.1，下面，我们以此镜像为基础创建并启动一个容器，如下：

```
docker run -it ubuntu:v1.1 cat /etc/os-release
```

那么容器只会执行cat /etc/os-release命令，也就是说在控制台只会输出系统版本信息，并不会输出Hello Docker!

举例说明两种写法：
```
CMD [ "sh", "-c", "echo $HOME" ]
CMD [ "echo", "$HOME" ]
```

补充细节：这里边包括参数的一定要用双引号，就是",不能是单引号。千万不能写成单引号。

原因是参数传递后，docker解析的是一个JSON array



#### ENTRYPOINT

ENTRYPOINT指令和CMD指令目的一样，都是指定容器运行程序及参数，并且与CMD一样拥有两种格式的写法：

```
# shell格式：ENTRYPOINT <命令>
# exec格式：ENTRYPOINT [“可执行文件”, “参数1”, “参数2”, …]
ENTRYPOINT ["executable", "param1", "param2"]
ENTRYPOINT command param1 param2
```


与CMD指令一样，ENTRYPOINT也更加推荐使用exec格式，ENTRYPOINT在docker run命令中同样也可以进行指定，只不过比CMD指令来的繁琐一些，需要指定--entrypoint参数。同样，在docker run命令中指定了--entrypoint参数的话，会覆盖Dockerfile中ENTRYPOINT上的指令。

当指定了ENTRYPOINT指令时，CMD指令里的命令性质将会发生改变！CMD指令中的内容将会以参数形式传递给ENTRYPOINT指令中的命令，如下：

```
FROM ubuntu
ENTRYPOINT ["rm", "docker2"]
CMD ["-rf"]
```

其实，它真正执行的命令将会是：

```
rm docker2 -rf
```


从例子中可以看出，ENTRYPOINT指令和CMD指令非常的相似，也很容易将其搞混，就比如上面的例子，就可以完全使用一条CMD指令CMD ["rm", "docker2", "-rf"]来完成。这两个指令到底有什么区别，为什么要同时保留这两条指令呢？

我们可以使用ENTRYPOINT指令和CMD指令相结合，使得在创建并启动时要执行的命令更加灵活！有如下Dockerfile：

```
FROM ubuntu
RUN apt-get update && apt-get install -y curl && rm -rf /var/lib/apt/lists/*
ENTRYPOINT ["curl", "-s", "http://ip.cn"]
```

此时，我们将其构建成镜像ubuntu:v1.2，下面我们创建并启动容器：

```
docker run -it ubuntu:v1.2
```

将会在控制台输出我们相应的公网IP信息！此时，如果我们还需要获取HTTP头信息时，我们可以这样：

```
docker run -it ubuntu:v1.2 -i
```

此时，将会在控制台中将公网IP信息以及HTTP头信息全部输出！我们知道，docker run命令中紧跟在镜像后面的是CMD指令命令，运行时会替换默认的CMD指令。因为我们在Dockerfile中指定了ENTRYPOINT指令，根据ENTRYPOINT指令的特性知道，当指定了ENTRYPOINT指令，CMD指令的内容将会以参数的形式传递给ENTRYPOINT，所以在容器中最终执行的命令是curl -s -i http://ip.cn，-i参数被传递到ENTRYPOINT中，所以最终在控制台中会输出HTTP头信息！！！



**ENTRYPOINT与CMD比较：**

1. 相同点：

- 如果写多条，只有最后一条生效。
- 容器启动时才运行，运行时机相同。

2. 不同点：

-  ENTRYPOINT不会被运行的CMD覆盖。
-  如果我们在Dockerfile种同时写了ENTRYPOINT和CMD，并且CMD指令不是一个完整的可执行命令，那么CMD指定的内容将会作为ENTRYPOINT的参数

如下：

```
FROM ubuntu
ENTRYPOINT ["top", "-b"]
CMD ["-c"]
```
- 如果我们在Dockerfile种同时写了ENTRYPOINT和CMD，并且CMD是一个完整的指令，那么它们两个会互相覆盖，谁在最后谁生效

如下：

```
FROM ubuntu
ENTRYPOINT ["top", "-b"]
CMD ls -al
```

那么将执行ls -al ,top -b不会执行。

 Docker官方使用一张表格来展示了ENTRYPOINT 和CMD不同组合的执行情况

略...



#### RUN & CMD

不要把RUN和CMD搞混了。

RUN是构件容器时就运行的命令以及提交运行结果

CMD是容器启动时执行的命令，在构件时并不运行，构件时紧紧指定了这个命令到底是个什么样子



#### LABEL

功能是为镜像指定标签

语法：`LABEL <key>=<value> <key>=<value> <key>=<value> ...`

一个Dockerfile种可以有多个LABEL，如下：

```
LABEL "com.example.vendor"="ACME Incorporated"
LABEL com.example.label-with-value="foo"
LABEL version="1.0"
LABEL description="This text illustrates \
that label-values can span multiple lines."
```

但是并不建议这样写，最好就写成一行，如太长需要换行的话则使用\符号

如下：

```
LABEL multi.label1="value1" \
multi.label2="value2" \
other="value3"
```

说明：LABEL会继承基础镜像种的LABEL，如遇到key相同，则值覆盖

LABEL后面是键值对，多个键值对以空格进行隔开，如果value中包含空格，请使用""将value进行圈起来，如下：

```
FROM ubuntu:16.04
LABEL name=test
LABEL description="a container is used to test"
```

我们知道，DockerFile的每一个指令都会新构建一层，所以，上面的LABEL我们可以写成一条指令，用空格进行隔开，如下：

```
FROM ubuntu:16.04
LABEL name=test description="a container is used to test"
```


为了美观，我们还可以使用\符号进行换行操作。

要查看镜像的标签，我们可以使用docker inspect命令，如下：

```
root@ubuntu:~# docker inspect --format '{{json .Config.Labels}}' test | python3 -m json.tool 
{
    "description": "a container is used to test",
    "name": "test"
}
```


其中“test”为容器名称！

值得注意的是，这里的标签并非是我们一开始将镜像名称中的<仓库>:<标签>，这两者是不一样的！这里标签，类似于签条，注解之类的意思



#### MAINTAINER

指定作者

语法：`MAINTAINER <name>`

MAINTAINER 指令用于指定生成镜像的作者名称，其格式为：`MAINTAINER <name>`

MAINTAINER指令已经被弃用，可以使用LABEL指令进行替代，如下：

```
LABEL maintainer='Stephen Chow'
```


MAINTAINER 指令在一些老的 Dockerfile 中仍然可以看到，所以还是需要了解一下的！



#### VOLUME

可实现挂载功能，可以将内地文件夹或者其他容器种得文件夹挂在到这个容器种

语法为：

```
VOLUME ["/data"]
```

说明：

  ["/data"]可以是一个JsonArray ，也可以是多个值。所以如下几种写法都是正确的

```
VOLUME ["/var/log/"]
VOLUME /var/log
VOLUME /var/log /var/db
```

定义一个匿名卷：

```
FROM ubuntu:16.04
VOLUME /data
```

定义多个匿名卷：

```
FROM ubuntu:16.04
VOLUME ["/data", "/command"]
```


这里的/data和/command目录在容器运行时会自动挂载为匿名卷，任何向/data和/command目录中写入的信息都不会记录进容器存储层，从而保证了容器存储层的无状态化！容器匿名卷目录指定可以通过docker run命令中指定-v参数来进行覆盖




#### USER

USER指令用于将会用以什么样的用户去运行，例如：

```
FROM ubuntu:16.04
USER docker
```

基于该镜像启动的容器会以docker用户的身份来运行，我们可以指定用户名或者UID，组名或者GID，或者两者的结合，如下：

```
FROM ubuntu:16.04
USER user
USER user:group
USER uid
USER uid:gid
USER user:gid
USER uid:group
```

USER 指令可以在 `docker run` 命令中的 `-u` 参数进行覆盖。
注意：如果设置了容器以daemon用户去运行，那么RUN, CMD 和 ENTRYPOINT 都会以这个用户去运行。



#### ONBUILD

语法：`ONBUILD [INSTRUCTION]`

这个命令只对当前镜像的子镜像生效，比如当前镜像为A，在Dockerfile种添加：

```
ONBUILD RUN ls -al
```

这个 `ls -al` 命令不会在A镜像构建或启动的时候执行；此时有一个镜像B是基于A镜像构建的，那么这个ls -al 命令会在B镜像构建的时候被执行。


ONBUILD是一个特殊的指令，它后面跟着的是其他指令，比如COPY、RUN等，而这些命令在当前镜像被构建时，并不会被执行。只有以当前镜像为基础镜像去构建下一级镜像时，才会被执行。格式为：ONBUILD <其他指令>

Dockerfile中的其他指令都是为了构建当前镜像准备的，只有ONBUILD指令是为了帮助别人定制而准备的。例如：

```
from ubuntu:16.04
WORKDIR /data
ONBUILD RUN mkdir test
```

此时，我们以此Dockerfile进行构建镜像ubuntu:test，并以此镜像为基础创建并启动一个容器，进入容器后，容器会自动切换到WORKDIR指令下的目录，此时我们使用ls命令会发现在工作目录下，并未创建test文件夹，如下：

```
root@ubuntu:~/docker# docker run -it ubuntu:test
root@3a8f912fd23b:/data# ls
root@3a8f912fd23b:/data#
```

此时，我们再创建一个Dockerfile，只需一个FROM指令即可，使其继承刚刚我们构建的ubuntu:test镜像，如下：

```
FROM ubuntu:test
```

我们再以此Dockerfile构建镜像ubuntu:test_onbuild，并以此镜像为基础创建并启动一个容器，进入容器后，容器会自动切换到WORKDIR指令下的目录，此时我们使用ls命令会发现在工作目录下，已经创建好了一个名为test的文件夹，如下：

```
root@ubuntu:~/docker# docker run -it ubuntu:test_onbuild
root@5394e605b6ea:/data# ls
test
```



#### STOPSIGNAL

语法：

```
STOPSIGNAL signal
```

STOPSIGNAL命令是的作用是当容器退出时给系统发送什么样的指令



#### HEALTHCHECK

HEALTHECHECK指令是告诉Docker该如何判断容器的状态是否正常，这是1.12引入的新指令，其格式有两种：

```
HEALTHCHECK [options] CMD <命令>：检查容器健康状态的命令
HEALTHCHECK NONE：如果基础镜像有健康检查指令，这一行将会屏蔽掉其健康检查指令
HEALTHECHECK支持下列选项：

–interval=<间隔>：两次检查的时间间隔，默认为30s
–timeout=<时长>：健康检查命令运行超时时间，如果超过这个时间，本次健康检查将会判定为失败，默认为30s
–retries=<次数>：当连续失败指定次数之后，则将容器状态视为unhealthy，默认为3次
```


在没有HEALTHCHECK指令之前，Docker引擎只可以通过容器内主进程是否退出来判断容器状态是否异常。很多情况下这没有问题，但是如果程序进入了死锁状态，或者死循环状态，应用进程并不退出，但是该容器已经无法继续提供服务了。在1.12之前，Docker引擎不会检测到容器的这种状态，从而不会重新调度，导致可能容器已经无法提供服务了却仍然还在接收用户的请求。

假设我们有个镜像是最简单的Web服务，我们希望增加健康检查来判断Web服务是否在正常工作，我们可以用curl来帮助判断，其 Dockerfile 的 `HEALTHCHECK` 可以这么写：

```
FROM nginx
RUN apt-get update && apt-get install -y curl && rm -rf /var/lib/apt/lists/*
HEALTHCHECK --interval=5s --timeout=3s CMD curl -fs http://localhost/ || exit 1
```

接下来，我们将该Dockerfile编译构建成一个镜像，并以此镜像为基础创建并启动一个容器。此时，我们使用 `docker container ls` 命令来查看容器的状态，如下：

```
root@ubuntu:~/docker# docker container ls
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                   PORTS                NAMES
036b91eea00d        nginx:v1.2          "nginx -g 'daemon of…"   7 seconds ago       Up 6 seconds (healthy)   0.0.0.0:80->80/tcp   web
```


我们再STATUS这一列中可以看到，状态未healthy。如果我们快速的多次执行docker container ls的话，会发现STATUS状态是由health: starting最后变为healthy，当然如果容器未在正常工作，最后的状态将会变为unhealthy

这里，我们设置了每5s检查一次，如果检查时间超过3s没有响应就视为失败。||符号左边的命令执行结果为假，右边的命令才会执行！

为了帮助排除故障，健康检查命令的输出会被存储于健康状态里，我们可以使用docker inspect命令来进行查看：

```
root@ubuntu:~/docker# docker inspect --format '{{json .State.Health}}' web | python3 -m json.tool
{
    "Status": "healthy",
    "FailingStreak": 0,
    "Log": [
        {
            "Start": "2018-07-17T21:15:05.900643297+08:00",
            "End": "2018-07-17T21:15:05.968989028+08:00",
            "ExitCode": 0,
            "Output": "<!DOCTYPE html>\n<html>\n<head>\n<title>Welcome to nginx!</title>\n<style>\n    body {\n width: 35em;\n margin: 0 auto;\n font-family: Tahoma, Verdana, Arial, sans-serif;\n }\n</style>\n</head>\n<body>\n<h1>Welcome to nginx!</h1>\n<p>If you see this page, the nginx web server is successfully installed and\nworking. Further configuration is required.</p>\n\n<p>For online documentation and support please refer to\n<a href=\"http://nginx.org/\">nginx.org</a>.<br/>\nCommercial support is available at\n<a href=\"http://nginx.com/\">nginx.com</a>.</p>\n\n<p><em>Thank you for using nginx.</em></p>\n</body>\n</html>\n"
        }
    ]
}
```


和CMD、NETRYPOINT一样，HEALTHCHECK指令只可以出现一次，如果有多个HEALTHCHECK指令，那么只有最后一个才会生效！！！


语法有两种：

```
1. HEALTHCHECK [OPTIONS] CMD command
2. HEALTHCHECK NONE
```

第一个的功能是在容器内部运行一个命令来检查容器的健康状况

第二个的功能是在基础镜像中取消健康检查命令

[OPTIONS]的选项支持以下三中选项：

  ***--interval=DURATION 两次检查默认的时间间隔为30秒***

  ***--timeout=DURATION 健康检查命令运行超时时长，默认30秒***

  ***--retries=N 当连续失败指定次数后，则容器被认为是不健康的，状态为unhealthy，默认次数是3***

  

注意：

HEALTHCHECK命令只能出现一次，如果出现了多次，只有最后一个生效。

CMD后边的命令的返回值决定了本次健康检查是否成功，具体的返回值如下：

***0: success - 表示容器是健康的***

***1: unhealthy - 表示容器已经不能工作了***

***2: reserved - 保留值***

例子：

```
HEALTHCHECK --interval=5m --timeout=3s \
CMD curl -f http://localhost/ || exit 1
```

健康检查命令是：curl -f http://localhost/ || exit 1

两次检查的间隔时间是5秒

命令超时时间为3秒



#### 参考
[Docker-DockerFile指令详解](https://blog.csdn.net/y472360651/article/details/81289141)
https://www.runoob.com/docker/docker-dockerfile.html
[[Dockerfile命令详解](https://www.cnblogs.com/lingfengblogs/p/11093246.html)](https://www.cnblogs.com/lingfengblogs/p/11093246.html)
[镜像多阶段构建](https://www.cnblogs.com/sparkdev/p/8508435.html)

[Dockerfile Caching](https://deploy-docs.aptible.com/docs/dockerfile-caching)

