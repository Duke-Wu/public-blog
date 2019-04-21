# Python：访问私有成员变量

Python 中将成员和方法私有化的方式是在成员名或者方法名前面加两个下划线，如下：

```python
class PrivateTest:
    __name = "private"


if __name__ == "__main__":
    pt = PrivateTest()
    print(pt.__name)
```

运行报错：

```
AttributeError: 'PrivateTest' object has no attribute '__name'
```

**Python 中访问私有成员变量的正确方式为：实例类._*类名__*变量名**

```python
class PrivateTest:
    __name = "private"


if __name__ == "__main__":
    pt = PrivateTest()
    print(pt._PrivateTest__name)
```

输出结果：

```
private
```

