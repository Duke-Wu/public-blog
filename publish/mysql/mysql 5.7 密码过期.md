---
title: "mysql 5.7 密码过期 ERROR 1862 (HY000): Your password has expired. To log in you must change"
date: 2019/1/1 0:0:0
updated: 2019/7/4 14:40:25
comments: true
tags:
- database
- mysql
categories:
- tool kits
---

## mysql 5.7 密码过期 ERROR 1862 (HY000): Your password has expired. To log in you must change

#### 一、mysql5.7 密码过期问题

报错：

`ERROR 1862 (HY000): Your password has expired. To log in you must change it using a client that supports expired passwords.`

翻译：

`错误1862(HY000):你的密码已经过期。登录必须改变它使用一个客户端,支持过期的密码。`



#### 解决方法：

1. 用忽略授权表的方法进入mysql 

   ```bash&#39;
   vi /etc/my.cnf
   [mysqld]
   skip-grant-tables
   :wq! #保存退出
   ```

2. 进入mysql，查看root用户的详细信息

   ```mysql
   # mysql -u root -p
   > use mysql
   > select * from mysql.user where user='root' \G
   ```

   ```bash
   mysql> select * from mysql.user where user='root' and host='localhost'\G
   *************************** 1. row ***************************
                     Host: localhost
                     User: root
              Select_priv: Y
              Insert_priv: Y
              Update_priv: Y
              Delete_priv: Y
              Create_priv: Y
                Drop_priv: Y
              Reload_priv: Y
            Shutdown_priv: Y
             Process_priv: Y
                File_priv: Y
               Grant_priv: Y
          References_priv: Y
               Index_priv: Y
               Alter_priv: Y
             Show_db_priv: Y
               Super_priv: Y
    Create_tmp_table_priv: Y
         Lock_tables_priv: Y
             Execute_priv: Y
          Repl_slave_priv: Y
         Repl_client_priv: Y
         Create_view_priv: Y
           Show_view_priv: Y
      Create_routine_priv: Y
       Alter_routine_priv: Y
         Create_user_priv: Y
               Event_priv: Y
             Trigger_priv: Y
   Create_tablespace_priv: Y
                 ssl_type: 
               ssl_cipher: 
              x509_issuer: 
             x509_subject: 
            max_questions: 0
              max_updates: 0
          max_connections: 0
     max_user_connections: 0
                   plugin: mysql_native_password
    authentication_string: *715B88E7FBB55622C2C9AA9662E586A9C8C68563
         password_expired: N
    password_last_changed: 2018-11-01 09:50:45
        password_lifetime: NULL
           account_locked: N
   1 row in set (0.01 sec)
   
   ```

3. 把password_expired 改成不过期

   ```bash
   >  update user set password_expired='N' where user='root';
   > flush privileges;
   > quit
   ```

4. 把 /etc/my.cnf 的 skip-grant-tables 这行注释掉

5. 重启服务 `service mysql restart`

6. 再次登陆 mysql 就正常了



