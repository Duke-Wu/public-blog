## nginx实现内容重定向的两种方式：rewrite和反向代理

Nginx 重定向后测试方法：https://blog.csdn.net/yrx420909/article/details/104536606




常用nginx rewrite重定向-跳转实例:

1. 将 www.myweb.com/connect  跳转到 connect.myweb.com
```
rewrite  ^/connect$       http://connect.myweb.com    permanent;
rewrite  ^/connect/(.*)$  http://connect.myweb.com/$1  permanent;
```

2. 将connect.myweb.com 301跳转到www.myweb.com/connect/ 
```
if ( $host = "connect.myweb.com" ){
    rewrite  ^/(.*)$  http://www.myweb.com/connect/$1  permanent;
}
```

3. myweb.com 跳转到www.myweb.com
```
if ($host != 'www.myweb.com' ) { 
    rewrite  ^/(.*)$  http://www.myweb.com/$1  permanent; 
}
```

4. www.myweb.com/category/123.html 跳转为 category/?cd=123
```
rewrite  "/category/(.*).html$"  /category/?cd=$1  last;
```

5. www.myweb.com/admin/ 下跳转为www.myweb.com/admin/index.php?s=
```
if (!-e $request_filename){
    rewrite ^/admin/(.*)$ /admin/index.php?s=/$1 last;
}
```


6. 在后面添加/index.php?s=
```
if (!-e $request_filename){
    rewrite  ^/(.*)$  /index.php?s=/$1  last;
}
```


7. www.myweb.com/xinwen/123.html  等xinwen下面数字+html的链接跳转为404
```
rewrite ^/xinwen/([0-9]+)\.html$ /404.html last;
```


8. http://www.myweb.com/news/radaier.html 301跳转 http://www.myweb.com/strategy/
```
rewrite ^/news/radaier.html http://www.myweb.com/strategy/ permanent;
```


9. 重定向 链接为404页面
```
rewrite http://www.myweb.com/123/456.php /404.html last;
```


10. 禁止htaccess
```
location ~//.ht {
    deny all;
}
```


11. 可以禁止/data/下多级目录下.log.txt等请求;
```
location ~ ^/data {
    deny all;
}
```


12. 禁止单个文件
```
location ~ /www/log/123.log {
    deny all;
}
```


13. http://www.myweb.com/news/activies/2014-08-26/123.html 跳转为 http://www.myweb.com/news/activies/123.html
```
rewrite ^/news/activies/2014\-([0-9]+)\-([0-9]+)/(.*)$ http://www.myweb.com/news/activies/$3 permanent;
```


14. nginx多条件重定向rewrite

如果需要打开带有play的链接就跳转到play，不过/admin/play这个不能跳转
```
if ($request_filename ~ (.*)/play){ set $payvar '1';}
if ($request_filename ~ (.*)/admin){ set $payvar '0';}
if ($payvar ~ '1'){
    rewrite ^/ http://play.myweb.com/ break;
}
```

15. http://www.myweb.com/?gid=6 跳转为http://www.myweb.com/123.html
```
 if ($request_uri ~ "/\?gid\=6"){return  http://www.myweb.com/123.html;}
```


正则表达式匹配，其中：

* ~ 为区分大小写匹配

* ~* 为不区分大小写匹配

* !~和!~*分别为区分大小写不匹配及不区分大小写不匹配

文件及目录匹配，其中：

* -f和!-f用来判断是否存在文件

* -d和!-d用来判断是否存在目录

* -e和!-e用来判断是否存在文件或目录

* -x和!-x用来判断文件是否可执行

flag标记有：

* last 相当于Apache里的[L]标记，表示完成rewrite

* break 终止匹配, 不再匹配后面的规则

* redirect 返回302临时重定向 地址栏会显示跳转后的地址

* permanent 返回301永久重定向 地址栏会显示跳转后的地址


