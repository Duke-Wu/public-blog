## MySQL基础操作

#### mysql 客户端和服务器端实用工具：
1. mysql：登录mysql数据库的客户端工具  
2. mysqldump：备份使用工具  
3. mysqlbinlog：查看binlog日志的工具  
4. mysqladmin：mysqladmin管理工具  
5. mysqld_safe：安全的启动mysqld服务  
6. mysql_install_db：初始化mysql数据库的工具  



#### 用SQL命令查看Mysql数据库大小

>步骤如下:
1. 进入 information_schema 数据库（存放了其他的数据库的信息）

`use information_schema;`

2. 查询所有数据的大小:

`SELECT concat(round(sum(DATA_LENGTH/1024/1024),2),'MB') as data FROM TABLES;`

3. 查看指定数据库的大小：
  比如查看数据库home的大小

`SELECT concat(round(sum(DATA_LENGTH/1024/1024),2),'MB') as data FROM TABLES WHERE table_schema='home';`

4. 查看指定数据库的某个表的大小
  比如查看数据库home中 members 表的大小

`SELECT concat(round(sum(DATA_LENGTH/1024/1024),2),'MB') as data FROM TABLES WHERE table_schema='home' AND table_name='members';`



#### 一些常见操作

```mysql
./bin/mysqladmin -uroot shutdown  #关闭数据库
./bin/mysqld --defaults-file=/etc/my.cnf --user=mysql  #带配置文件启动

mysql -e " commands "  #登录并执行命令
show variables; 或者 show variables like "%bin%";
show status;
show processlist; 
SELECT DATABASE();  显示正在使用的数据库名
SELECT USER();  显示当前用户

备份单个的表
mysqldump -u用户名 数据库  表名称 > /home/temp/aaa.sql     

查看MYSQL数据库中所有用户
mysql> SELECT DISTINCT CONCAT('User: ''',user,'''@''',host,''';') AS query FROM mysql.user;

查看数据库中具体某个用户的权限
mysql> show grants for 'cactiuser'@'%';  
select * from mysql.user where user='cactiuser' \G 

查看user表结构　需要具体的项可结合表结构来查询
mysql> desc mysql.user;

show variables like 'autocommit';
或者select @@autocommit;		SHOW VARIABLES LIKE '%AUTOCOMMIT%';		SHOW GLOBAL STATUS LIKE '%AUTOCOMMIT%';
查看commit提交属性。
set global  autocommit=0; 	#设置MySQL全局属性

select * from mysql.user where user='viewuser'  \G;

service mysqld start	启动mysql服务
service mysqld restart	重启mysql服务
\q					退出并关闭连接
mysql> \s			查看版本信息
mysql -h127.0.0.1 -uroot -p wuran  连接mysql数据库，并进入wuran数据库

导入.sql文件命令：
mysql> USE 数据库名;
mysql> SOURCE d:/mysql.sql;
```

如果不知道MySQL当前使用配置文件(my.cnf)，可以尝试下面的操作：
```bash
# which mysqld
/usr/local/mysql/bin/mysqld
# /usr/local/mysql/bin/mysqld --verbose --help | grep -A 1 'Default options'
2016-06-02 16:49:39 0 [Note] /usr/local/mysql/bin/mysqld (mysqld 5.6.25-log) starting as process 8253 ...
2016-06-02 16:49:41 8253 [Note] Plugin 'FEDERATED' is disabled.
Default options are read from the following files in the given order: 
/etc/mysql/my.cnf /etc/my.cnf ~/.my.cnf 
```



#### 安全停止mysql数据库：

mysql启动方式有很多种，根据系统和安装方式不一样，启动方法也不一样。

#### 解压版
启动和停止方法：

```bash
/usr/local/mysql/bin/mysqld_safe –user=mysql 
/usr/local/mysql/bin/mysqladmin -uroot -p shutdown (然后输入密码) 
（或者直接 kill 掉进程）
```

-user=mysql 是为了设置告诉mysql，生成的数据的访问权限属于哪个用户。



#### 修改密码的四种方法

1. 用 SET PASSWORD 命令

   首先登录MySQL。 
   格式：`mysql> set password for 用户名@localhost = password('新密码'); `
   例子：`mysql> set password for root@localhost = password('123'); `

2. 用 mysqladmin

   格式：`mysqladmin -u用户名 -p旧密码 password 新密码 `
   例子：`mysqladmin -uroot -p123456 password 123 `

3. 用 UPDATE 直接编辑user表

   首先登录MySQL。 

   ```mysql
   mysql> use mysql; 
   mysql> update user set authentication_string=password('123') where user='root' and host='localhost'; 
   // mysql> update user set password=password('123') where user='root' and host='localhost';  (MySQL 版本不同，运行的命令也不一样)
   mysql> flush privileges; 
   ```

4. 在忘记 root 密码的时候，可以这样

   1. 关闭正在运行的MySQL服务。 

   2. 打开cmd窗口，转到mysql\bin目录。 

   3. 输入`mysqld --skip-grant-tables` 或者 ` mysqld_safe --skip-grant-tables`(mysql 版本差异)。`--skip-grant-tables` 的意思是启动mysql服务的时候跳过权限表认证。

   4. 再开一个cmd窗口（因为刚才那个cmd窗口已经不能动了），转到mysql\bin目录。 

   5. 输入mysql回车，如果成功，将出现MySQL提示符 >。 

   6. 连接权限数据库： use mysql; 。 

   7. 改密码：`update user set password=password("123") where user="root";`

   8. 刷新权限（必须步骤）:`flush privileges;`

   9. 退出 quit。 

   10. 注销系统，再进入，使用用户名root和刚才设置的新密码登录。



#### MAC安装版
启动和停止方法：

````bash
/usr/local/Cellar/mysql/bin/mysql.server start 
/usr/local/Cellar/mysql/bin/mysql.server stop

#### Linux安装版
Linux版本不一样，启动方式也有点不太一样。

/etc/init.d/mysqld start 
/etc/init.d/mysqld stop 
/etc/init.d/mysqld restart 
或 
service mysqld start 
service mysqld stop 
service mysqld restart 
或 
service mysql start 
service mysql stop 
service mysql restart
​```
````

