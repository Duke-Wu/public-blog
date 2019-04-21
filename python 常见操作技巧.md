# python 常见操作技巧

## python返回列表中指定内容的索引

```
import numpy as np
a=[2,10,2,3,4,10,10]
ans = np.where(np.array(a)==10)
print(ans)
```

结果是：(array([1, 5, 6], dtype=int64),)

就是说，np.where()可以返回同一个元素在不同位置的索引。

```
a=[2,10,2,3,4,10,10]
ans = a.index(10)
print(ans)
```

结果是：1

就是说，a.index()返回指定元素第一个位置的索引。



## 索引迭代

用特殊函数进行索引迭代，实际上仍然是普通迭代，不过是一次迭代多个元素；Python中，迭代永远是取出元素本身，而非元素的索引。

```
方法是使用 enumerate() 函数：

>>> L = ['Adam', 'Lisa', 'Bart', 'Paul']
>>> for index, name in enumerate(L):
...     print index, '-', name
... 
0 - Adam
1 - Lisa
2 - Bart
3 - Paul
```

<https://www.cnblogs.com/superxuezhazha/p/5712253.html>