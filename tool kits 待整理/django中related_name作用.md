
#### ManyToManyField


#### OneToOneField

#### ForeignKey

[`ManyToManyField`](https://docs.djangoproject.com/en/2.1/ref/models/fields/#django.db.models.ManyToManyField) and [`OneToOneField`](https://docs.djangoproject.com/en/2.1/ref/models/fields/#django.db.models.OneToOneField).







原因：

```
class Apple( models.Model):
	origin_level = models.ForeignKey(AppleLevel)
	new_level = models.ForeignKey(AppleLevel)
```

一个数据表同时两次外键引用同一个表，使用 "xxx_set" 出现重名问题。

解决办法：
使用related_name属性定义名称(related_name是关联对象反向引用描述符)。
具体修改代码如下：

```
class Apple( models.Model):
	origin_level = models.ForeignKey(AppleLevel, related_name='orgin_level_appleset')
	new_level = models.ForeignKey(AppleLevel, related_name='new_level_appleset')
```

