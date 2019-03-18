django+nginx+uwsgi+supervisor+daphne+docker deploying websocket



## 配置文件简介

#### asgi.py

在setting.py的路径下添加asgi.py

```python
import os
from channels.asgi import get_channel_layer
os.environ.setdefault("DJANGO_SETTINGS_MODULE", "projects_name.settings")
channel_layer = get_channel_layer()
```

#### nginx.conf

```python
server {
    .....
    location / {
        uwsgi_pass  projects_name;
        include    /etc/nginx/uwsgi_params; 
    }
    location /ws/ {
            proxy_pass http://127.0.0.1:8000/ws/;
            proxy_redirect off;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
            proxy_set_header Host $host;
        }

}
```

#### uwsgi.ini

```
[uwsgi]
chdir           = /usr/src/app 
module          = projects_name.wsgi
master          = true
processes       = 10
socket          = /tmp/projects_name.sock
chmod-socket    = 666
vacuum          = true
enable-threads  = true
```

#### supervisord.conf

```python
[program:app-worker]
command =python /usr/src/app/manage.py runworker #其实这里感觉怪怪的，有时候查查是否有更好的解决方法
autostart=true
autorestart=true
stdout_logfile=/var/log/supervisor/%(program_name)s.log
stderr_logfile=/var/log/supervisor/%(program_name)s.log

[program:app-websocket]
command = daphne -b 0.0.0.0 projects_name.asgi:channel_layer
autostart=true
autorestart=true
stdout_logfile=/var/log/supervisor/%(program_name)s.log
stderr_logfile=/var/log/supervisor/%(program_name)s.log
```

#### 测试方式

```
wscat -c ws://IP+port/api
```



