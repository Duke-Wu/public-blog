## Questions

I'm doing a pipeline code refactoring using python.

Assuming we have a series of **generator** functions and we want to chain those to form a data processing pipeline. 

Example:

```
#!/usr/bin/python
import itertools

def foo1(g):
    for i in g:
        yield i + 1

def foo2(g):
    for i in g:
        yield 10 + i

def foo3(g):
    for i in g:
        yield 'foo3:' + str(i)

res = foo3(foo2(foo1(range(0, 5))))

for i in res:
    print i
```

Output:

```
foo3:11
foo3:12
foo3:13
foo3:14
foo3:15
```


I'm doing a pipeline code refactoring using python.

Assuming we have a series of **generator** functions and we want to chain those to form a data processing pipeline. 

Example:

```py
#!/usr/bin/python
import itertools

def foo1(g):
    for i in g:
        yield i + 1

def foo2(g):
    for i in g:
        yield 10 + i

def foo3(g):
    for i in g:
        yield 'foo3:' + str(i)

res = foo3(foo2(foo1(range(0, 5))))

for i in res:
    print i
```

Output:

```py
foo3:11
foo3:12
foo3:13
foo3:14
foo3:15
```

I do not think `foo3(foo2(foo1(range(0, 5))))` is a pythonic way to achieve my pipeline goal. Especially when the number of stages in the pipeline is large.

I wish I could rewrite it like chain in jquery. Something similar to :

```py
range(0, 5).foo1().foo2().foo3()
```

Or maybe

```py
l = [range(0, 5), foo1, foo2, foo3]
res = runner.run(l)
```

But I'm new to generator topic and couldn't find a way to achieve this.

Any help will be welcome.

## Answers

I sometimes like to use a left fold (called `reduce` in Python) for this type of situation:

```py
from functools import reduce
def pipeline(*steps):
    return reduce(lambda x, y: y(x), list(steps))

res = pipeline(range(0, 5), foo1, foo2, foo3)
```

Or even better:

```py
def compose(*funcs):
    return lambda x: reduce(lambda f, g: g(f), list(funcs), x)

p = compose(foo1, foo2, foo3)
res = p(range(0, 5))
```



## 备注

**copy from** <https://stackoverflow.com/questions/38755702/pythonic-way-to-chain-python-generator-function-to-form-a-pipeline>

[How to join two generators in Python?](https://stackoverflow.com/questions/3211041/how-to-join-two-generators-in-python)