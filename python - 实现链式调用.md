# python - 实现链式调用

用过jquery的一般都知道在jquery中可以链式调用，代码简洁优雅。比如$(“a”).addClass(“test”).show().html(“foo”);。

在redis-py中的pipeline中也可以链式调用，比如pipe.set('foo', 'bar').sadd('faz', 'baz').incr('auto_number').execute() 。

那么究竟怎么实现的呢？

很简单，返回对象自己就行了，即return self.

废话不多说，上代码。
```
class Person:
    def name(self, name):
        self.name = name
        return self

    def age(self, age):
        self.age = age
        return self
     
    def show(self):
        print "My name is", self.name, "and I am", self.age, "years old."

p = Person()
p.name("Li Lei").age(15).show()
```