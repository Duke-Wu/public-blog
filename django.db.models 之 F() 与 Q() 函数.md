---
title: django.db.models 之 F() 与 Q() 函数
date: 2019/3/24 11:36:25
---

# django.db.models 之 F() 与 Q() 函数

## 前提：

app名称为core，models.py 如下：

```
#coding: utf8
import datetime
from django.db import models

class Order(models.Model):
    orderid     = models.CharField(max_length=64, unique=True)
    desc        = models.CharField(max_length=512)
    product     = models.CharField(max_length=512, null=True)
    amount      = models.IntegerField()
    userid      = models.CharField(max_length=512, null=True)
    create_time = models.DateTimeField(db_index=True)
```

## 1. F() ---- 专门取对象中某列值的操作

F()允许Django在未实际链接数据的情况下具有对数据库字段的值的引用。通常情况下我们在更新数据时需要先从数据库里将原数据取出后方在内存里，然后编辑某些属性，最后提交。例如

```
order = Order.objects.get(orderid='123456789')
order.amount += 1
order.save()
```

上述方法生成的sql语句为（可以使用connection.queries查看<http://www.cnblogs.com/liuq/p/5946166.html>）：

```
UPDATE `core_order` SET ..., `amount` = 22 WHERE `core_order`.`orderid` = '123456789' # ...表示Order中的其他值，在这里会重新赋一遍值; 22表示为计算后的结果
```

其实想生成的sql语句为：

```
UPDATE `core_order` SET ..., `amount` = `amount` + 1 WHERE `core_order`.`orderid` = '123456789'
```

这时就可以使用F()方法，代码如下：

```
from django.db.models import F
from core.models import Order
 
order = Order.objects.get(orderid='123456789')
order.amount = F('amount') - 1
order.save()
```

查看生成的sql：

```
UPDATE `core_order` SET ..., `amount` = `core_order`.`amount` - 1 WHERE `core_order`.`orderid` = '123456789' # 和预计的一样
```

当Django程序中出现F()时，Django会使用SQL语句的方式取代标准的Python操作。

上述代码中不管 `order.amount` 的值是什么，Python都不曾获取过其值，python做的唯一的事情就是通过Django的F()函数创建了一条SQL语句然后执行而已。

需要注意的是在使用上述方法**更新过数据之后需要重新加载数据来使数据库中的值与程序中的值对应**：

```
 order = Order.objects.get(pk=order.pk)
```

或者使用更加简单的方法：

```
order.refresh_from_db()
```

这里只做引子，详细参考如下：

博客：<http://www.tuicool.com/articles/u6FBRz>

官网：<https://docs.djangoproject.com/en/1.10/ref/models/expressions/>

## 2. Q() ---- 对对象的复杂查询

Q对象(django.db.models.Q)可以对关键字参数进行封装，从而更好地应用多个查询。可以组合使用 &（and）,|（or），~（not）操作符，当一个操作符是用于两个Q的对象,它产生一个新的Q对象。

```
Order.objects.get(
    Q(desc__startswith='Who'),
    Q(create_time=date(2016, 10, 2)) | Q(create_time=date(2016, 10, 6))
)
```

转换成sql语句，大致如下：

```
SELECT * from core_order WHERE desc LIKE 'Who%' AND (create_time = '2016-10-02' OR create_time = '2016-10-06')
```

Q对象可以与关键字参数查询一起使用，不过**一定要把Q对象放在关键字参数查询的前面**。

正确写法：

```
Order.objects.get(
    Q(create_time=date(2016, 10, 2)) | Q(create_time=date(2016, 10, 6))
    desc__startswith='Who',
)
```

错误写法：

```
Order.objects.get(
    desc__startswith='Who',
    Q(create_time=date(2016, 10, 2)) | Q(create_time=date(2016, 10, 6))
)
```

详细内容可查看下面地址：

博客：<http://www.cnblogs.com/linjiqin/p/3817814.html>

官网：<https://docs.djangoproject.com/en/1.10/topics/db/queries/#complex-lookups-with-q>



## 参考：

http://www.cnblogs.com/liuq/p/5946803.html