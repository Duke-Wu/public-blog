Yes, it is possible if the results are buffered outside the generator. One easy way is to use [*itertools.tee()*](https://docs.python.org/2.7/library/itertools.html#itertools.tee):

```py
>>> from itertools import tee
>>> def three_line_gen():
        yield 0
        yield 1
        yield 2

>>> t1, t2 = tee(three_line_gen())
>>> next(t1)
0
>>> next(t2)
0
>>> list(t1)
[1, 2]
>>> list(t2)
[1, 2]
```

