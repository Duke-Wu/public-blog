# Python 拷贝对象(深拷贝deepcopy与浅拷贝copy）

1. copy.copy 浅拷贝 只拷贝父对象，不会拷贝对象的内部的子对象。

2. copy.deepcopy 深拷贝 拷贝对象及其子对象

一个很好的例子：

```
# -*-coding:utf-8 -*-
import copy
a = [1, 2, 3, 4, ['a', 'b']] #原始对象
 
b = a #赋值，传对象的引用
c = copy.copy(a) #对象拷贝，浅拷贝
d = copy.deepcopy(a) #对象拷贝，深拷贝
 
a.append(5) #修改对象a
a[4].append('c') #修改对象a中的['a', 'b']数组对象
 
print 'a = ', a
print 'b = ', b
print 'c = ', c
print 'd = ', d
```

输出结果：

a =  [1, 2, 3, 4, ['a', 'b', 'c'], 5]

b =  [1, 2, 3, 4, ['a', 'b', 'c'], 5]

c =  [1, 2, 3, 4, ['a', 'b', 'c']]

d =  [1, 2, 3, 4, ['a', 'b']]