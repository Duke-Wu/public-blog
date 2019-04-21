# 跨域请求结果中header的权限控制Access-Control-Expose-Headers

在使用angular7 HttpClient实现文件下载功能时，需要将filename放入response的header中，但页面无法获取,查了下是因为涉及跨域。

**stackflow中找到方法:**

<https://stackoverflow.com/questions/28805589/how-to-read-response-headers-in-angularjs>

大意为：在涉及跨域请求时，response中大部分header需要源服务端同意才能拿到,所以需要在response中增加一个如下header：

```
Access-Control-Expose-Headers: content-type, cache, ...
```

其中"content-type, cache, ..."为允许跨域访问的header的key,多个时用英文逗号隔开



**Github 相同的问题**

<https://github.com/angular/angular/issues/13226>



#### Django 后端代码示例

后端使用 Django REST framework 框架。

```python
import os
from django.http import StreamingHttpResponse
# ......代码省略......

class VersionPackageViewSet(GenericViewSet, CreateModelMixin):
    # ......代码省略......
    
    def create(self, request, *args, **kwargs):
        file_name = 'file_for_download.tgz'
        file_path = '/tmp/file_for_download.tgz'
        file_len = os.path.getsize(file_path)
        response = StreamingHttpResponse(self.file_iterator(file_path))
        response['Content-Type'] = 'application/octet-stream'
        response['Content-Length'] = file_len
        response['Content-Disposition'] = 'attachment;filename="{0}"'.format(file_name)
        response["Access-Control-Allow-Origin"] = "*"
        response["Access-Control-Expose-Headers"] = "content-type,content-disposition"
        super().create(request, *args, **kwargs)
        return response

    @staticmethod
    def file_iterator(file_name, chunk_size=2048):
        with open(file_name, 'rb') as f:
            while True:
                c = f.read(chunk_size)
                if c:
                    yield c
                else:
                    break
                    
```



#### [How to set Content-Disposition header using Django Rest Framework](https://stackoverflow.com/questions/53887763/how-to-set-content-disposition-header-using-django-rest-framework)

According to [this page](https://docs.djangoproject.com/en/2.1/ref/request-response/#telling-the-browser-to-treat-the-response-as-a-file-attachment) in the Django docs, you can set the `Content-Disposition` header in this way:

```
response = Response(my_data, content_type='image/jpeg')
response['Content-Disposition'] = 'attachment; filename="foo.jpeg"'
```



#### [How to add an HTTP header to all Django responses](https://stackoverflow.com/questions/36099244/how-to-add-an-http-header-to-all-django-responses)

I'd like to add a few headers to all responses that my Django website returns. Is there a way to do this (besides adding a wrapper to the render function)?
Yes, you should have a look at middlewares.

yourapp/middleware.py

```python
class MyMiddleware:

    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        response = self.get_response(request)
        response['X-My-Header'] = "my value"
        return response
```

*yourproject/settings.py*

```python
MIDDLEWARE = [
    ...,
    'yourapp.middleware.MyMiddleware',
    ...,
]
```



#### [Custom HTTP Header in Django](https://stackoverflow.com/questions/14377050/custom-http-header-in-django)

I use a custom http header for URL signature just called "sign", how to get such custom HTTP header value in Django?

**Answer:**

Go ahead and use:

```
request.META.get('HTTP_{your uppercased header name}')
```

Note in Django you write the header name in capitals with underscores instead of dashes, but in the request on the client you must write it using dashes instead of underscores (production web servers will strip out custom headers with underscores in them for security reasons).

So, a custom header `My-Custom-Header` is accessed `request.META['HTTP_MY_CUSTOM_HEADER']`

