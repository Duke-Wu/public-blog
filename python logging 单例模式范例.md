### 方案一
https://blog.51cto.com/reboot001/1732450
```python
#logger.py
#author: Zilu.Tang
#date: 20160107
import logging
import os


class SingleLogger(object):
    __instance = None

    def __init__(self):
        pass

    def __new__(cls, *args, **kwd):
        if SingleLogger.__instance is None:
            SingleLogger.__instance = object.__new__(cls, *args, **kwd)
            SingleLogger.__instance.__logger = logging.getLogger("logger1")
            SingleLogger.__instance.__logger.setLevel(logging.DEBUG)
            formatter = logging.Formatter('%(name)-12s %(asctime)s %(levelname)-8s %(message)s', '%a, %d %b %Y %H:%M:%S',)
            file_handler = logging.FileHandler(r"test.log")
            file_handler.setLevel(logging.INFO)
            stream_handler = logging.StreamHandler()
            file_handler.setFormatter(formatter)
            SingleLogger.__instance.__logger.addHandler(file_handler)
            SingleLogger.__instance.__logger.addHandler(stream_handler)
            SingleLogger.__instance.__logger.info("log info: " + os.getcwd())
        return SingleLogger.__instance

    def info(self, message):
        SingleLogger.__instance.__logger.info(message)
```

Usage：

```python
from logger import SingleLogger
class Usage():
    def __init__(self):
        self.__logger = SingleLogger()
    
    def test(self):
        self.__logger.info("Test logger")
```



### 方案二
<https://blog.csdn.net/caoxinjian423/article/details/83211932>

```python
#!/usr/bin/env python
# coding:UTF-8

"""
@version: python3.x
@author:曹新健
@contact: 617349013@qq.com
@software: PyCharm
@file: 单例模式装饰日志类.py
@time: 2018/10/18 15:10
"""
import os,logging,sys,time

def singleton(cls):
  instances = {}
  def _singleton(*args,**kwargs):
    if cls not in instances:
      instances[cls] = cls(*args,**kwargs)
    return instances[cls]
  return _singleton

@singleton
class Logger():
    def __init__(self,logfile=None):
        self.logger = logging.getLogger()
        formater = logging.Formatter('%(asctime)s %(name)s  %(levelname)s %(filename)s  %(lineno)d '
                        '%(thread)d %(threadName)s %(process)d %(message)s')
        if logfile == None:
            cur_path = os.path.split(os.path.realpath(__file__))[0]
            stime = time.strftime("%Y-%m-%d",time.localtime())
            logfile = cur_path + os.sep + "log_" + stime + ".log"
        else:
            logfile = logfile
        self.sh = logging.StreamHandler(sys.stdout)
        self.sh.setFormatter(formater)
        self.fh = logging.FileHandler(logfile)
        self.fh.setFormatter(formater)
        self.logger.addHandler(self.sh)
        self.logger.addHandler(self.fh)
        self.logger.setLevel(logging.WARNING)

if __name__ == "__main__":
    lg = Logger()
    lg.logger.warning("aaa1")
```

