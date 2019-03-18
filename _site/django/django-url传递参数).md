url传递参数有两种，一个是通过普通分组方式，一个是通过带命名分组方式

#### 1. 普通分组方式，传递参数顺序是严格的。如下例子

url(r'^detail-(\d+)-(\d+).html',views.detail), 共传递了两个参数

def detail(request,nid,uid)第一个(\d+)传递给了nid,第二个（\d+）传递uid，

但是如果变为detail(request,uid,nid),则第一个(\d+)传递给了uid,所以可能会存在传递错误问题



#### 2. 带命名分组

url(r'^detail-(?P<nid>\d+)-(?P<uid>\d+).html', views.detail) 第一个参数一直是传递给nid,第二个参数一直传递给uid，所以在视图中不管nid,uid顺序如何，都能传递正确。

> **推荐使用带命名分组**



#### 3. 传递的参数很多怎么办？可以用如下方式

a 方案：

```python
url(r'^detail-(\d+)-(\d+).html', views.detail),
                
def func(request, nid, uid):
	pass

def func(request, *args):
	args = (2,9)

def func(request, *args, **kwargs):
	args = (2,9)
　　
```

b 方案：

```python
url(r'^detail-(?P<nid>\d+)-(?P<uid>\d+).html', views.detail)

def func(request, nid, uid):
	pass

def funct(request, **kwargs):
	kwargs = {'nid': 1, 'uid': 3}

def func(request, *args, **kwargs):
	args = (2,9)
```



#### 4. 渲染页面是动态和静态页面

通常我们认为带？参数页面为动态的。比如：http://127.0.0.1:8001/detail/?id=3

动态页面对搜索爬虫不太友好。百度等静态页面更容易收录。可以改静态传递值http://127.0.0.1:8001/detail-3（这里就需要使用命名分组）



转载自：https://www.cnblogs.com/lixiang1013/p/7748156.html

博主其它文章：https://www.cnblogs.com/lixiang1013/category/1103148.html