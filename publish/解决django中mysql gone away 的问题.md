---
layout: post
title: "解决 django 中 mysql gone away 的问题"
date: 2019-03-18 13:35:57 +0800
categories: jekyll update
---


# 解决 django 中 mysql gone away 的问题

## 多进程连接Mysql异常

场景：

我遇到的情况是Django下多进程连接数据库异常，设置为每次连数据库前先断开之前的连接就可以了。

报错：

(2013, 'Lost connection to MySQL server during query') 

(2006, 'MySQL server has gone away') 

分析：

解决：

from django import db

db.close_old_connections()

rh = ReleaseHistory(name="itnul.com")

rh.save()





## Django ORM 数据库生命周期

### Django 如何做的

这个问题归根到底是「如何管理数据库连接的生命周期」的问题。

一个数据库连接，一头系着 client （即 App，这里我们简化为 Django ），一头系着 server （MySQL Server），其状态是由这两者加上其他因素（姑且先排除）决定的。client 这端自然好控制，自始至终都在我们的控制范围之内，然而 server 那端就不好控制了，加上 my.cnf 各种奇奇怪怪的配置，皆可影响 server 的行为，所以 server 在我们的控制范围之外。

在 Web App 中，数据库连接的生命周期一般和 HTTP Request 的生命周期一致，即：在 Request 开始时，初始化好连接（一般从 Connection Pool 中取一个）；在 Request 结束后，将连接释放（ 一般是还回到 Pool 中 ）。但这是比较理想的情况，「连接到底是不是可用，到底是不是好的」这个问题没有解决。

Django 的做法是：利用其信号机制（Django 中的一种解耦手段），在 Request 开始和结束的时候，将不可用的连接清理掉，从而确保整个 Request 周期内，所取到的连接都是没问题的。至于如何判断一个连接的可用与不可用，方法是多种多样的，比如 MySQL，可以 ping 下 server，看连接是否正常。

Diango的处理代码如下：

```
django\db\__init__.py

# 其实是一个 callback
def close_old_connections(**kwargs):
    for conn in connections.all():
        conn.close_if_unusable_or_obsolete()


# 信号注册
# 可以看到, 主要核心逻辑在于 close_old_connections
signals.request_started.connect(close_old_connections)
signals.request_finished.connect(close_old_connections)
```



### 针对我的需求，我的做法

我们的项目重度使用 Django，简化的逻辑如下

```
Web Server( 对外提供 REST API )                     Thrift Server( 对内部提供RPC API )
 ............................
           ORM Model
           DB
```

其中，Web Server 这块自然不用多说，各种需要操心的细节，Django 都替我们管理好了。主要是 Thrift Server 这里，它也是直接使用 Django 的 ORM Model，所以也需要实现一套「连接管理的信号注册」。

根据前面的说明，我们只需要实现 Thrift Request 开始和结束时的信号注册即可，即：如法炮制，调用 close_old_connections 。但我们有一个针对 Thrift handler 的装饰器，我发现只需要在那里面调用 close_old_connections 就可以了。

大致逻辑如下

```
def decorator(method):
    def _wrapper(self, *args):
        try:
            # 开始时，清理无效连接
            close_old_connections()
            
            method_result = method(self, *args)
        exception Exception as e:
            raise ThriftServerException(e)
        else:
            return method_result
        finally:
            # 结束后，清理无效连接 （似乎必要性不大 ？ ）
            close_old_connections()
            
    return _wrapper
```

Thrift Hanlder 使用：

```
class Handler(Iface):
    @decorator
    def interface1(self, request):
        xxx
```



### 启示

框架一般封装了太多东西，抽象出来的层级数目也很多，一般如果需要实现自己非常个性化的需求，就比较难受了，需要深入到框架的源码中去，才能有效解决问题。所以正如厨师需要熟悉他的工具一样，开发者也需要熟悉他依赖的框架，用 Django 就要熟悉 Django，用 Spring 就要熟悉 Spring 。



### 参考链接

---

参考链接1：[https://zhaojames0707.github.io/post/django_mysql_gone_away/](https://zhaojames0707.github.io/post/django_mysql_gone_away/)

参考链接2：[https://juejin.im/post/5a7876b2f265da4e761faad2](https://juejin.im/post/5a7876b2f265da4e761faad2)

参考链接3：[https://www.jianshu.com/p/bf68318160e6](https://www.jianshu.com/p/bf68318160e6)

参考链接4：[https://www.jianshu.com/p/bf68318160e6](https://www.jianshu.com/p/bf68318160e6)