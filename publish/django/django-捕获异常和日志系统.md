## 使用装饰器捕获方法内的所有异常

我使用装饰器来整个包裹一个方法, 捕获方法中的所有异常信息.并将其转为json返回客户端.

```python
import functools

def catch_exception(func, code=500, *args, **kwargs):
    '''
    :param func:
    :return:
    '''

    @functools.wraps(func, *args, **kwargs)
    def nefen(request, *args, **kwargs):
        try:
            back = func(request, *args, **kwargs)
            return back
        except Exception as e:
            # string = "捕获到异常"
            # x = type(e)
            #
            # if x == ValueError:
            #     string = "数值转换异常:" + str(e)
            return JsonError(error_string=str(e), code=code)

    return nefen
```

JsonError是之前编写的json工具.

装饰器的使用方法如下.

```python
class ReturnJson(APIView):

    coreapi_fields=(
        DocParam("token"),
    )
    
    @catch_exception
    def get(self, request, *args, **kwargs):
        params=get_parameter_dic(request)
        return JsonResponse(data=params)
    
    @catch_exception
    def post(self, request, *args, **kwargs):
        params=get_parameter_dic(request)
        return JsonResponse(data=params)
    
    @catch_exception
    def put(self, request, *args, **kwargs):
        params=get_parameter_dic(request)
        return JsonResponse(data=params)
```

### 日志配置

```python
# 首先创建日志存储路径.
import logging
import django.utils.log
import logging.handlers

log_path = os.path.join(BASE_DIR, "logs")

if not os.path.exists(log_path):
    os.makedirs("logs")

# DJANGO_LOG_LEVEL=DEBUG

LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'formatters': {
        'verbose': {
            'format': '%(levelname)s %(asctime)s %(module)s %(process)d %(thread)d %(message)s'
        },
        'simple': {
            'format': '%(levelname)s %(message)s'
        },
        'standard': {
            'format': '%(asctime)s [%(threadName)s:%(thread)d] [%(name)s:%(lineno)d] [%(levelname)s]- %(message)s'
        },
    },
    'handlers': {
        'default': {
            'level':'DEBUG',
            'class':'logging.handlers.RotatingFileHandler',
            'filename': os.path.join(BASE_DIR,'logs','all.log'), #或者直接写路径：'c:\logs\all.log',
            'maxBytes': 1024*1024*5, # 5 MB
            'backupCount': 5,
            'formatter':'standard',
        },
        'console': {
            'level': 'DEBUG',
            'class': 'logging.StreamHandler',
            'formatter': 'standard',
        },
        'file': {
            'level':'INFO',
            'class':'logging.handlers.RotatingFileHandler',
            'filename': os.path.join(BASE_DIR, 'logs','debug.log'), #或者直接写路径：'c:\logs\all.log',
            'maxBytes': 1024*1024*5, # 5 MB
            'backupCount': 5,
            'formatter':'standard',
        },
    },
    # DEBUG(测试环境) CRITICAL(项目崩溃)   ERROR(抛出异常未被捕获)  WARNING(例如403)  INFO(系统表现相关)
    'loggers': {
        'print': {
            'handlers': ["file"],
            'level': 'INFO',
            'propagate': False
        },
        'ifacerecognition': {
            'handlers': ['default'],
            'level': 'ERROR',
        },
        # 'django': {
        #     'handlers': ['default'],
        #     'level': os.getenv('DJANGO_LOG_LEVEL', 'INFO'),
        # },
        'django.request': {
            'handlers': ['default'],
            'level': 'ERROR',
        },
    },
}
```

日志配置由三部分组成
1.日志格式formatters
2.日志处理方法, 例如保存到xxx.log文件或者别的什么, 甚至可以自动发送电子邮件.
3.日志器, 也就是正式的应用, 你可以获取一个log, 手动添加log内容.

一个向log文件写入内容的例子

```python
import logging
class ReturnJson(APIView):

    coreapi_fields=(
        DocParam("token"),
    )

    @catch_exception
    def get(self, request, *args, **kwargs):
        params=get_parameter_dic(request)
        log=logging.getLogger("print")
        log.info("asdf")
        log.error("asdffff")
        return JsonResponse(data=params)
```


作者：行如风
链接：https://www.jianshu.com/p/04287164a274
來源：简书
简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。