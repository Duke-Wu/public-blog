## Django 之密码加密
通过django自带的类库，来加密解密很方便，下面来简单介绍下；

导入包：

```python
from django.contrib.auth.hashers import make_password, check_password
```


从名字就可以看出来他们的作用了，一个是生成密码，一个是核对密码。

例如：

```python
make_password("123456")
# 得到结果：
u'pbkdf2_sha25615000MAjic3nDGFoi$qbclz+peplspCbRF6uoPZZ42aJIIkMpGt6lQ+Iq8nfQ='
```


另外也可以通过参数来生成密码：

```python
make_password("123456", None, 'pbkdf2_sha256')
```



#### 校验

校验就是通过check_password(原始值, 生成的密文)来校验密码的。

```python
check_password("123456","pbkdf2_sha25615000MAjic3nDGFoi$qbclz+peplspCbRF6uoPZZ42aJIIkMpGt6lQ+Iq8nfQ=")
True
```

