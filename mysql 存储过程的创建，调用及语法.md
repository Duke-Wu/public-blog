### mysql查看存储过程函数

#### 查询数据库中的存储过程和函数
```
select `name` from mysql.proc where db = 'xx' and `type` = 'PROCEDURE'   //存储过程
select `name` from mysql.proc where db = 'xx' and `type` = 'FUNCTION'   //函数

show procedure status; //存储过程
show function status;     //函数
```

#### 查看存储过程或函数的创建代码
```
show create procedure proc_name;
show create function func_name;
```

#### 查看视图
```
SELECT * from information_schema.VIEWS   //视图
SELECT * from information_schema.TABLES   //表
```

#### 查看触发器
```sql
SHOW TRIGGERS [FROM db_name] [LIKE expr]
SELECT * FROM triggers T WHERE trigger_name=”mytrigger” \G
```

### MYSQL中存储过程的创建，调用及语法
MySQL 存储过程是从 MySQL 5.0 开始增加的新功能。存储过程的优点有一箩筐。不过最主要的还是执行效率和SQL 代码封装。特别是 SQL 代码封装功能，如果没有存储过程，在外部程序访问数据库时（例如 PHP），要组织很多 SQL 语句。特别是业务逻辑复杂的时候，一大堆的 SQL 和条件夹杂在 PHP 代码中，让人不寒而栗。现在有了 MySQL 存储过程，业务逻辑可以封装存储过程中，这样不仅容易维护，而且执行效率也高。

#### 第一部分：创建一个简单的无参的存储过程
1. 用mysql客户端登入

2. 选择数据库
  mysql>use test;

3. 查询当前数据库有哪些存储过程
  mysql>show procedure status where Db='test';

4. 创建一个简单的存储过程
  mysql>create procedure hi() select 'hello';

5. 存储过程创建完毕,看怎么调用它
   mysql>call hi();
   显示结果
   ```
   mysql> call hi();
   +-------+
   | hello |
   +-------+
   | hello |
   +-------+
   1 row in set (0.01 sec)
    
   Query OK, 0 rows affected (0.01 sec)
   ```
6. 一个简单的储存过程就成功了,这只是一个演示,存储过程可以一次执行多个sql语句



#### 第二部分：创建一个有两个参数的存储过程

##### 一、MySQL 创建存储过程 
“pr_add” 是个简单的 MySQL 存储过程，这个存储过程有两个 int 类型的输入参数 “a”、“b”，返回这两个参数的和。 
drop procedure if exists pr_add;
-- 计算两个数之和

```
create procedure pr_add
(
   a int,
   b int
)
begin
   declare c int;
   if a is null then
      set a = 0;
   end if;
   if b is null then
      set b = 0;
   end if;
   set c = a + b;
   select c as sum;
   
end;
```



##### 二、调用 MySQL 存储过程 

call pr_add(10, 20);
执行 MySQL 存储过程，存储过程参数为 MySQL 用户变量。 
set @a = 10;
set @b = 20;
call pr_add(@a, @b);



##### 三、MySQL 存储过程特点 
创建 MySQL 存储过程的简单语法为： 
create procedure 存储过程名字()
(
   [in|out|inout] 参数 datatype
)
begin
   MySQL 语句;
end;

MySQL 存储过程参数如果不显式指定“in”、“out”、“inout”，则默认为“in”。习惯上，对于是“in” 的参数，我们都不会显式指定。 

1. MySQL 存储过程名字后面的“()”是必须的，即使没有一个参数，也需要“()” 
2. MySQL 存储过程参数，不能在参数名称前加“@”，如：“@a int”。下面的创建存储过程语法在 MySQL 中是错误的（在 SQL Server 中是正确的）。 MySQL 存储过程中的变量，不需要在变量名字前加“@”，虽然 MySQL 客户端用户变量要加个“@”。 
  create procedure pr_add
  (
   @a int,  -- 错误
   b int    -- 正确
  )
3. MySQL 存储过程的参数不能指定默认值。 
4. MySQL 存储过程不需要在 procedure body 前面加 “as”。而 SQL Server 存储过程必须加 “as” 关键字。 
  create procedure pr_add
  (
   a int,
   b int
  )
  as              -- 错误，MySQL 不需要 “as”
  begin
   mysql statement ...;
  end;
5. 如果 MySQL 存储过程中包含多条 MySQL 语句，则需要 begin end 关键字。 
  create procedure pr_add
  (
   a int,
   b int
  )
  begin
   mysql statement 1 ...;
   mysql statement 2 ...;
  end;
6. MySQL 存储过程中的每条语句的末尾，都要加上分号 “;” 
   ...
   declare c int;
   if a is null then
      set a = 0;
   end if;
   ...
   end;
7. MySQL 存储过程中的注释。 

   declare c int;     -- 这是单行 MySQL 注释 （注意 -- 后至少要有一个空格）
   if a is null then  # 这也是个单行 MySQL 注释
      set a = 0;
   end if;
   ...
   end;
8. 不能在 MySQL 存储过程中使用 “return” 关键字。 
   set c = a + b;
   select c as sum;


end;
9. 调用 MySQL 存储过程时候，需要在过程名字后面加“()”，即使没有一个参数，也需要“()”
  call pr_no_param();
10. 因为 MySQL 存储过程参数没有默认值，所以在调用 MySQL 存储过程时候，不能省略参数。可以用 null 来替代。 
  call pr_add(10, null);

### 备注

**copy from <https://www.cnblogs.com/you-zi/p/5519006.html>**


　　