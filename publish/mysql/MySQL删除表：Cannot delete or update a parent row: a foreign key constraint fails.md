---
title: MySQL删除表：Cannot delete or update a parent row/ a foreign key constraint fails
date: 2019/1/1 0:0:0
updated: 2019/7/4 14:40:25
comments: true
tags:
- database
- mysql
categories:
- tool kits
---

## MySQL删除表：Cannot delete or update a parent row/ a foreign key constraint fails

![winter](http://img-note.wuqianlin.cn/img-md/2018-12-26-095405.png)

MySQL库中有俩表，table1和table2，相互关联，在删除表的时候出错：

Cannot delete or update a parent row: a foreign key constraint fails。

很明显这是表关联生成的强制约束问题，在删除的时候回检查表之间的关联关系，从而导致无法删除，解决办法：

```
SET foreign_key_checks = 0;  // 先设置外键约束检查关闭
drop table table1;  // 删除表，如果要删除视图，也是如此
SET foreign_key_checks = 1; // 开启外键约束检查，以保持表结构完整性
```

MySQL的环境变量中存在一个foreign_key_checks，这是默认检查外键的配置项，如果将其设置为0，则表示不检查外键约束。查看foreign_key_checks的值：

```
show VARIABLES like "foreign%";
```

运行结果：

```sql
mysql> show variables like "foreign%";
+--------------------+-------+
| Variable_name      | Value |
+--------------------+-------+
| foreign_key_checks | OFF    |
+--------------------+-------+
1 row in set, 1 warning (0.01 sec)
```

这是因为我将foreign_key_checks的值设置为了0，所以这里显示的是OFF，为关闭状态。运行SET foreign_key_checks = 1;将其设置为1，那这里的状态将是ON。



注意：在删除完表之后，最好是重新打开检查(SET foreign_key_checks = 1) 以保持表格结构的完整性。

参考链接：https://stackoverflow.com/questions/2300396/force-drop-mysql-bypassing-foreign-key-constraint?rq=1
