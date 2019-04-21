# net-snmp 开发过程问题俩三事

## 一、snmp 使用DEBUGMSGTL，进行debug调试信息

### 1. 修改 /net-snmp-5.6.1.1/include/net-snmp/Net-snmp-config.h文件

在 Net-snmp-config.h 文件中，注释掉 `NETSNMP_NO_DEBUGGING` 的宏定义，并把 `NETSNMP_ALWAYS_DEBUG` 的值改为 0，才能使 `-Dtoken` 生效。

```c
/* debugging stuff */
/* if defined, we optimize the code to exclude all debugging calls. */
//#define NETSNMP_NO_DEBUGGING 1
/* ignore the -D flag and always print debugging information */
#define NETSNMP_ALWAYS_DEBUG 0
```

举例，`DEBUGMSGTL(("trap", "sending trap %ld\n",count))`，这句的调试token是"trap"，使用命令 `./snmpd -f -Dtrap` 可以只输出token为trap的所有调试信息。

snmpd 启动时默认会fork一个进程执行，因此，shell中是看不到任何输出的，可以加 -f，表示do not fork from the shell，这样才会看到snmpd的输出信息。snmpd -h 可以查看所有的选项，-f是这些当中比较实用的。



### 2.注意事项：
源码编译包时，使能 debugging  ./configure  --enable-debugging 



### 3.使用启动方法：
DEBUGMSGTL(("xxx", "sending trap %ld\n",count));

snmpd -c /etc/snmp/snmp.conf -d -f  -L  -Dxxx

-d 打印收到和发送的数据包
-L 指定日志输出位置

具体使用方法详见：`snmpd -h`



### 4.参考

NET-SNMP开发——日志输出：https://www.bbsmax.com/A/Vx5MLbE7JN/

Debug tokens 5.7.2：http://net-snmp.sourceforge.net/wiki/index.php/Debug_tokens_5.7.2

Enabling Debug logs is net-snmp：https://sourceforge.net/p/net-snmp/mailman/message/27728001/



## 二、How to make Net-SNMP threadsafe

### 1. Can I use AgentX (or an embedded SNMP agent) in a threaded application?

With care.

As mentioned in the earlier "thread-safe" FAQ entry, the Net-SNMP agent (including the AgentX subagent) has not been designed for threaded operation. In particular, it makes use of various global variables without attempting to protect them against simultaneous use. This means that it is **NOT** safe to have SNMP or AgentX related processing in two separate threads. This also applies to handling GET (and SET) processing in one thread, and generating traps in another. This is still vulnerable to the usual threading problems.

However, as long as **all** of the SNMP-related activity is limited to the one thread, then there should be no reason why this cannot safely communicate with other threads within the same application, using private (thread-safe) mechanisms.

But in terms of the Net-SNMP-provided code, the agent (and AgentX subagent) should **not** be regarded as thread-safe.



### 3.参考

How to make Net-SNMP threadsafe：https://sourceforge.net/p/net-snmp/mailman/message/15881152/



## 三、net-snmp 专家 vincent 的文章和demo

Recipes for extending Net-SNMP：https://vincent.bernat.ch/en/blog/2012-extending-netsnmp

Asynchronicity *&* Net-SNMP AgentX protocol：https://vincent.bernat.ch/en/blog/2012-fixing-async-agentx

Integration of Net-SNMP into an event loop：https://vincent.bernat.ch/en/blog/2012-snmp-event-loop

vincentbernat/**ethtool-snmpd**：https://github.com/vincentbernat/ethtool-snmpd