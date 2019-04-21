
## 让带有 N 个参数的可调用对象以较少的参数形式调用-functools.partial() 


```python
from functools import partial

def spam(a, b, c, d):
    print(a, b, c, d)
s1 = partial(spam, 1)    # a = 1
s1(2,3,4)
```

    1 2 3 4



```python
s1(4,5,6)
```

    1 4 5 6



```python
s2 = partial(spam, d=42) 
s2(4, 5, 6)
```

    4 5 6 42



```python
s3 = partial(spam, 1, 2, d=42)
s3(3)
```

    1 2 3 42



```python
s3(4)
```

    1 2 4 42



```python
s3(5)
```

    1 2 5 42



```python
points = [(1,2), (3,4),(5,6),(7,8)]

import math
def distance(p1, p2):
    x1, y1 = p1
    x2, y2 = p2
    return math.hypot(x2 - x1, y2 - y1)

pt=(4, 3)
points.sort(key=partial(distance, pt))
print(points)
```

    [(3, 4), (1, 2), (5, 6), (7, 8)]

