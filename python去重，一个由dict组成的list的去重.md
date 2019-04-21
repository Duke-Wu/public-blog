# python去重，一个由dict组成的list的去重

背景：有一个list，里面的每一个元素都是dict，根据某一个key进行去重，在这里，key代表question

```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
# created by fhqplzj on 2017/12/07 上午11:38
from itertools import groupby
from operator import itemgetter
 
import pandas as pd
 
 
def distinct(items):
    questions = map(itemgetter('question'), items)
    df = pd.DataFrame({
        'items': items,
        'questions': questions
    })
    return df.drop_duplicates(['questions'])['items'].tolist()
 
 
def distinct2(items):
    exist_questions = set()
    result = []
    for item in items:
        question = item['question']
        if question not in exist_questions:
            exist_questions.add(question)
            result.append(item)
    return result
 
 
def distinct3(items):
    key = itemgetter('question')
    items = sorted(items, key=key)
    return [next(v) for _, v in groupby(items, key=key)]
 
 
def distinct4(items):
    from itertools import compress
    mask = (~pd.Series(map(itemgetter('question'), items)).duplicated()).tolist()
    return list(compress(items, mask))
 
 
if __name__ == '__main__':
    data = [
        {'question': 'a', 'ans': 'b'},
        {'question': 'b', 'ans': 'd'},
        {'question': 'a', 'ans': 'p'},
        {'question': 'b', 'ans': 'e'}
    ]
    print distinct4(data)
```

---------------------
作者：asd991936157 
来源：CSDN 
原文：https://blog.csdn.net/ASD991936157/article/details/78740610 
版权声明：本文为博主原创文章，转载请附上博文链接！





# [python列表里的字典元素去重](https://www.cnblogs.com/robinunix/p/9942102.html)



**阅读目录(Content)**

- [python列表中元素去重的几种方式](http://www.cnblogs.com/robinunix/p/9942102.html#_label0)

去重

```
`def list_dict_duplicate_removal():``    ``data_list = [{``"a"``: ``"123"``, ``"b"``: ``"321"``}, {``"a"``: ``"123"``, ``"b"``: ``"321"``}, {``"b"``: ``"321"``, ``"a"``: ``"123"``}]``    ``run_function = lambda x, y: x ``if` `y ``in` `x ``else` `x + [y]``    ``return` `reduce(run_function, [[], ] + data_list)`  `if` `__name__ == ``'__main__'``:``    ``print list_dict_duplicate_removal()`
```

输出结果：

[{'a': '123', 'b': '321'}]

 

# python列表中元素去重的几种方式

```
`class` `StringReverse(``object``):``    ``''``'``    ``列表去重，并按照原来的顺序排序``    ``''``'` `    ``# 1.利用set方法和sort方法，原序``    ``def string_duplicate_1(self, s):``        ``new_s = list(``set``(s))  # ``set``无序``        ``new_s.sort(key=s.index)``        ``return` `new_s`  `    ``# 2.用列表中的元素作为字典中的key生成一个新字典，然后获取字典的key，非原序``    ``def string_duplicate_2(self, s):``        ``a = {}``        ``# fromkeys(s,v)该方法的功能是生成一个字典，字典的key是 s中的值，s为可迭代对象，可以为str,tuple,list,set,dict，v为每一个key的值，默认为None``        ``return` `a.fromkeys(s).keys()`  `    ``# 3.利用defaultdict, 非原序``    ``def string_duplicate_3(self, s):``        ``# 按照之前的顺序``        ``from` `collections import defaultdict``        ``a = defaultdict()``        ``for` `x ``in` `s:``            ``a[x] = 0``        ``return` `a.keys()` `    ``# 4.最简单的循环，添加入新的列表，如果新列表中没有相同元素，则加入。原序``    ``def string_duplicate_4(self, s):``        ``new_s = []``        ``for` `x ``in` `s:``            ``if` `x not ``in` `new_s:``                ``new_s.append(x)``        ``return` `new_s`  `    ``# 5.利用itertools的groupby方法。非原序``    ``def string_duplicate_5(self, s):``        ``from` `itertools import groupby``        ``s.sort()``        ``new_groupby = groupby(s)``        ``new_s = []``        ``for` `x,y ``in` `new_groupby:``            ``new_s.append(x)``        ``return` `new_s` `    ``# 6.reduce方法。非原序``    ``def string_duplicate_6(self, s):``        ``return` `reduce(lambda x,y:x ``if` `y ``in` `x ``else` `x + [y], [[],] + s)` `if` `__name__ == ``"__main__"``:``    ``stringReverse = StringReverse()``    ``s = [1,3,2,34,4,6,6,7,1,4,8,98]``    ``print ``"string_duplicate_1"``, stringReverse.string_duplicate_1(s)``    ``print ``"string_duplicate_2"``, stringReverse.string_duplicate_2(s)``    ``print ``"string_duplicate_3"``, stringReverse.string_duplicate_3(s)``    ``print ``"string_duplicate_4"``, stringReverse.string_duplicate_4(s)``    ``print ``"string_duplicate_5"``, stringReverse.string_duplicate_5(s)``    ``print ``"string_duplicate_6"``, stringReverse.string_duplicate_6(s)  `
```

输出结果为：

```
`string_duplicate_1 [1, 3, 2, 34, 4, 6, 7, 8, 98]``string_duplicate_2 [1, 2, 3, 4, 98, 6, 7, 8, 34]``string_duplicate_3 [1, 2, 3, 4, 98, 6, 7, 8, 34]``string_duplicate_4 [1, 3, 2, 34, 4, 6, 7, 8, 98]``string_duplicate_5 [1, 2, 3, 4, 6, 7, 8, 34, 98]``string_duplicate_6 [1, 2, 3, 4, 6, 7, 8, 34, 98]`
```