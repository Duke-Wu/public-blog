### Fabric 2.0

Fabric is a high level Python (2.7, 3.4+) library designed to execute shell commands remotely over SSH, yielding useful Python objects in return:

### Fabric3

Fabric3 is a Python (2.7 or 3.4+) library and command-line tool for streamlining the use of SSH for application deployment or systems administration tasks. This is a fork of the original [Fabric](http://www.fabfile.org/) ([git](https://github.com/fabric/fabric)) with the intention of providing support for Python3, while maintaining support for all non-archaic versions of Python2.



### Differences between Fabric and Fabric3

Generally this project aims to be a drop-in replacement for Fabric and will periodically merge any changes from the upstream project. Any differences are noted here:

- The release installs as Fabric3. Despite its name, this version is tested with Python2.7 and Python 3.4+.
- Versioning is based on upstream Fabric releases, with a postX appended. So version "1.12.0.post1" is equivalent to Fabrics own "1.12.0" release.
- `fabric.utils.RingBuffer` is removed, use `collections.deque` from the standard library instead.
- In Python3, Fabric3 implements its own version of `contextlib.nested` based on `contextlib.ExitStack`, since it's no longer available in Python3. Please note that it was removed with good reason, we do not encourage you use it.
- Fabric3 requires the six library for compatibility.
- Minimum requirements for paramiko have been bumped to 1.17.0.





### 命令说明

```
格式
fab [options] <command>[:arg1,arg2=val2,host=foo,hosts='h1;h2',...]...

常用参数
-l  显示定义好的任务函数名
-f  指定fab入口文件，默认入口文件名为fabfile.py
-g 指定网关（中转）设备，比如堡垒机环境，填写堡垒机ip即可
-H 指定目标主机，多台主机用","号分隔
-P 以异步并行方式运行多主机任务，默认是串行运行
-p 远程帐号的密码，fab执行时默认使用root账户
-R 指定roel（角色），以角色名区分不同业务组设备
-t 设置设备连接超时时间（秒）
-T 设置远程主机命令执行超时时间（秒）
-w 当命令执行失败，发出警告，而非默认中止任务
```

### fabfile文件编写
fab命令是结合fabfile.py文件（其他文件通过-f filename参数来引用）来搭配使用的。fab的部分命令行参数能通过fabfile env 对象来设置。

#### fabfile之env对象

```
env对象的最用是定义fabfile的全局设定，就像上面的举例，下面对个属性进行说明：
env.hosts：           定义目标主机，可以用IP或主机名表示，以python的列表形式定义。如env.hosts=["192.168.88.2", "192.168.88.3"]
env.exclude_hosts:    排除指定主机，如env.exclude_hosts=['192.168.88.2']
env.user              定义用户名，如env.user='root'
env.port              定义端口，默认为22，如env.port='22'
env.password          定义密码，如env.password='123.com'
env.passwords         定义多个密码，不同主机对应不同密码，如：env.passwords={'root@192.168.88.2:22':'123.com', 'root@192.168.88.2:22':'123456'}
env.gateway           定义网关（中转，堡垒机）ip，如env.gateway='192.168.88.10'
env.roledefs          定义角色组，比如web组合db组主机区分开来env_roledefs={'webserver':['192.168.88.2', '192.168.88.3'], 'dbserver':['192.168.88.4']}
env.deploy_release_dir 自定义全局变量，格式：env. + '变量名'， 如env.age,  env.sex等
```

#### env.roledefs的使用方法实例

```
#!/usr/bin/env python
# _*_ coding:utf-8 _*_

from fabric.api import *

env.user = 'root'
env.password = 'password'
env.roledefs = {'web': ['192.168.0.1'], 'db': ['192.168.0.2']}

@roles('web')
def webtask():
    run('hostname')

@role('db')
def dbtask():
    run('hostname')
```



#### Fabric常用API

```
local   执行本地命令，如local('uname -s')
lcd     切换本地目录，如lcd('/home')
cd      切换远程目录
run     执行远程命令
sudo  sudo   方式执行远程命令，如sudo('/etc/init.d/httpd start')
put          上传本地文件到远程主机，如put('/home/user.info', '/data/user.info')
get          从远程主机下载文件到本地，如get('/data/user.info', '/home/user.info')
prompt       获得用户输入信息，如prompt('please input user password:')
confirm      获得提示信息确认，如confirm('Test faild, Continue[Y/N]?')
reboot       重启远程主机，如reboot()
 
@task        函数装饰符，标识的函数为fab可调用，非标记对fab不可见，纯业务逻辑
@runs_once   函数装饰符，标识的函数只会执行一次，不受多台主机影响
```



### 参考文章

Fabric git：https://github.com/fabric/fabric/

Fabric官网：https://www.fabfile.org/

Upgrading from fabric 1.x：http://www.fabfile.org/upgrading.html#upgrading

Fabric3 git：https://github.com/mathiasertl/fabric

https://www.cnblogs.com/sellsa/p/6733343.html