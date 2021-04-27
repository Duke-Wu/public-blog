# Postgresql高可用方案



## Architecture

### 1. Operating System

```
[root@pgpool01 ~]# cat /etc/os-release 
NAME="CentOS Linux"
VERSION="7 (Core)O
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
PRETTY_NAME="CentOS Linux 7 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:7"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"

CENTOS_MANTISBT_PROJECT="CentOS-7"
CENTOS_MANTISBT_PROJECT_VERSION="7"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT_VERSION="7"

```

### 2. Hostname and IP address

| Hostname     | IP Address  | Virtual IP  |
| ------------ | ----------- | ----------- |
| pgpool01     | 192.168.0.2 | 192.168.0.6 |
| pgpool02     | 192.168.0.3 |             |
| postgresql01 | 192.168.0.4 |             |
| postgresql02 | 192.168.0.5 |             |

### 3. Configure Hosts
Edit hosts file
```
cat >> /etc/hosts << EOT
192.168.0.2    pgpool01
192.168.0.3    pgpool02
192.168.0.4    postgresql01
192.168.0.5    postgresql02
EOT
```

### 4. Configure ssh passwordless on all servers

```bash
[root@pgpool01 ~]# cd ~/.ssh
[root@pgpool01 ~]# ssh-keygen -t rsa -f id_rsa_pgpool
[root@pgpool01 ~]# ssh-copy-id -i id_rsa_pgpool.pub postgres@pgpool01
[root@pgpool01 ~]# ssh-copy-id -i id_rsa_pgpool.pub postgres@pgpool02
[root@pgpool01 ~]# ssh-copy-id -i id_rsa_pgpool.pub postgres@postgresql01
[root@pgpool01 ~]# ssh-copy-id -i id_rsa_pgpool.pub postgres@postgresql02

[root@pgpool01 ~]# su - postgres
[root@pgpool01 ~]# cd ~/.ssh
[root@pgpool01 ~]# ssh-keygen -t rsa -f id_rsa_pgpool
[root@pgpool01 ~]# ssh-copy-id -i id_rsa_pgpool.pub postgres@pgpool01
[root@pgpool01 ~]# ssh-copy-id -i id_rsa_pgpool.pub postgres@pgpool02
[root@pgpool01 ~]# ssh-copy-id -i id_rsa_pgpool.pub postgres@postgresql01
[root@pgpool01 ~]# ssh-copy-id -i id_rsa_pgpool.pub postgres@postgresql02
```



## Install 

### 1. Install PostgreSQL 12.5：

```bash
sudo yum install -y https://download.postgresql.org/pub/repos/yum/reporpms/EL-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm
sudo yum install -y postgresql12-server
sudo /usr/pgsql-12/bin/postgresql-12-setup initdb
sudo systemctl enable postgresql-12
sudo systemctl start postgresql-12
psql --version    # display PostgreSQL version
psql (PostgreSQL) 12.5

# In order to use the online recovery functionality, we need install follow:
yum install -y https://www.pgpool.net/yum/rpms/4.1/redhat/rhel-7-x86_64/pgpool-II-release-4.1-2.noarch.rpm
yum install -y pgpool-II-pg12-extensions
```

### 2. Install pgpool-II-release-4.1.5：

> https://www.pgpool.net/docs/latest/en/html/install-rpm.html

```bash
yum install -y https://www.pgpool.net/yum/rpms/4.1/redhat/rhel-7-x86_64/pgpool-II-release-4.1-2.noarch.rpm
yum install -y pgpool-II-pg12
yum install -y pgpool-II-pg12-debuginfo
yum install -y pgpool-II-pg12-devel
# On all the PostgreSQL servers you need to install:
yum install -y pgpool-II-pg12-extensions    # not install, don't need
pgpool --version    # display pgpool version
# pgpool-II version 4.1.5 (karasukiboshi)

# Starting/stopping Pgpool-II
mkdir -p /var/log/pgpool    # create log directory
chown -R postgres:postgres /var/log/pgpool    # 赋予相应读写权限
systemctl enable pgpool.service
systemctl start pgpool.service 

# install postgresql client for getting the test command
sudo yum install -y https://download.postgresql.org/pub/repos/yum/reporpms/EL-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm
sudo yum install -y postgresql10  
```

### 3. Resolve the problems

Encounter an error startup pgpool "FATAL:  failed to bind a socket: "/var/run/postgresql/.s.PGSQL.9999""，

You can do some modify "pgpool.conf" file to fix it.

Change default setting

> socket_dir = '/var/run/postgresql'
> pcp_socket_dir = '/var/run/postgresql'
> wd_ipc_socket_dir = '/var/run/postgresql'

to

> socket_dir = '/tmp'
> pcp_socket_dir = '/tmp'
> wd_ipc_socket_dir = '/tmp'

Save then restart pgpool.



## Configure

> https://www.pgpool.net/docs/pgpool-II-4.1.5/en/html/example-cluster.html

### 1. Set up PostgreSQL streaming replication on the primary server
>In this project, we use WAL archiving.

```bash
# Edit the config file /var/lib/pgsql/12/data/postgresql.conf with follow content
listen_addresses = '*'
archive_mode = on
archive_command = 'cp "%p" "/var/lib/pgsql/archivedir/%f"'
max_wal_senders = 10
max_replication_slots = 10
wal_level = replica
hot_standby = on
wal_log_hints = on
```

Because of the security reasons, we create a user `repl` solely used for replication purpose, and a user `pgpool` for streaming replication delay check and health check of Pgpool-II.

| User Name | Password | Detail                                                       |
| --------- | -------- | ------------------------------------------------------------ |
| repl      | repl     | PostgreSQL replication user                                  |
| pgpool    | pgpool   | Pgpool-II health check (health_check_user) and replication delay check (sr_check_user) user |
| postgres  | postgres | User running online recovery                                 |

```bash
[server1]# sudo -u postgres psql postgres
postgres=# SET password_encryption = 'scram-sha-256';
postgres=# CREATE ROLE pgpool WITH LOGIN;
postgres=# CREATE ROLE repl WITH REPLICATION LOGIN;
postgres=# \password pgpool
postgres=# \password repl
postgres=# \password postgres
```

If you want to show "replication_state" and "replication_sync_state" column in [SHOW POOL NODES](https://www.pgpool.net/docs/latest/en/html/sql-show-pool-nodes.html) command result, role `pgpool` needs to be PostgreSQL super user or or in `pg_monitor` group (Pgpool-II 4.1 or later). Grant `pg_monitor` to `pgpool`:

```bash
GRANT pg_monitor TO pgpool;
```



### 2. Postgresql and Pgpool manage command:

```bash
systemctl status postgresql-12
systemctl start postgresql-12
systemctl stop postgresql-12
systemctl restart postgresql-12
journalctl -f -u  postgresql-12.service

systemctl status pgpool.service
systemctl start pgpool.service
systemctl restart pgpool.service
systemctl stop pgpool.service
journalctl -f -u  pgpool.service
```



## The way to judge if a postgresql server is master or slave

### 1. Display the database cluster state

```bash
[root@postgresql01 ~]# /usr/pgsql-12/bin/pg_controldata -D /var/lib/pgsql/12/data/ | grep 'Database cluster state'
Database cluster state:               in archive recovery     # postgresql server is slave

[root@postgresql02 data]# /usr/pgsql-12/bin/pg_controldata -D /var/lib/pgsql/12/data/ | grep 'Database cluster state'
Database cluster state:               in production            # postgresql server is master
```

### 2. Display the walsender or walreceiver status

```bash
[root@postgresql02 data]# ps -ef | grep postgres | grep 'walsender\|walreceiver' | grep -v grep
postgres 25856 21767  0 11:14 ?        00:00:00 postgres: walsender repl 192.168.0.4(48546) streaming 0/C01F358
# display "walsender", the server is master

[root@postgresql01 ~]# ps -ef | grep postgres | grep 'walsender\|walreceiver' | grep -v grep
postgres  7822  7646  0 11:14 ?        00:00:00 postgres: walreceiver   streaming 0/C01F358
# display "walreceiver", the server is slave
```

### 3. Execute pg_is_in_recovery function
```
postgres=# select pg_is_in_recovery();
 pg_is_in_recovery 
-------------------
 f
(1 row)
# display f is master

postgres=# select pg_is_in_recovery();
 pg_is_in_recovery 
-------------------
 t
(1 row)
# display t is slave
```

### 4. Select from master server table pg_stat_replication

```
postgres=# select pid,application_name,client_addr,client_port,state,sync_state from pg_stat_replication;

  pid | application_name | client_addr | client_port |  state  | sync_state

 ------+------------------+-------------+-------------+-----------+------------

  1401 | walreceiver   | 10.1.83.135 |    48860 | streaming | async
```
> 在主机字典表中是能查到记录，备机中是查询不到的。



## Verify that system are working normally

Run the follow command at any server (pgpool01, pgpool02, postgresql01, postgresql02):

```bash
# 
[root@pgpool-01 pgpool-II]# psql -h 192.168.0.6 -p 9999 -U pgpool postgres -c "show pool_nodes"
Password for user pgpool: 
 node_id |   hostname   | port | status | lb_weight |  role   | select_cnt | load_balance_node | replication_delay | replication_state | re
plication_sync_state | last_status_change  
---------+--------------+------+--------+-----------+---------+------------+-------------------+-------------------+-------------------+---
---------------------+---------------------
 0       | postgresql01 | 5432 | up     | 0.500000  | standby | 0          | false             | 0                 |                   |   
                     | 2020-12-22 11:54:14
 1       | postgresql02 | 5432 | up     | 0.500000  | primary | 0          | true              | 0                 |                   |   
                     | 2020-12-22 11:54:14
(2 rows)
[root@pgpool-01 pgpool-II]# pcp_watchdog_info -h 192.168.0.6 -p 9898 -U pgpool
Password: 
2 YES 192.168.0.2:9999 Linux pgpool-01 192.168.0.2

192.168.0.2:9999 Linux pgpool-01 192.168.0.2 9999 9000 4 MASTER
192.168.0.3:9999 Linux pgpool-02 192.168.0.3 9999 9000 7 STANDBY
```

**Stop the pgpool-01's pgpool master**

```
[root@pgpool-01 pgpool-II]# systemctl stop pgpool.service
[root@pgpool-01 pgpool-II]# pcp_watchdog_info -h 192.168.0.6 -p 9898 -U pgpool
Password: 
2 YES 192.168.0.3:9999 Linux pgpool-02 192.168.0.3

192.168.0.3:9999 Linux pgpool-02 192.168.0.3 9999 9000 4 MASTER
192.168.0.2:9999 Linux pgpool-01 192.168.0.2 9999 9000 10 SHUTDOWN
[root@pgpool-01 pgpool-II]# 
```

**Start the pgpool-01's pgpool service**

```bash
[root@pgpool-01 pgpool-II]# systemctl start pgpool.service
[root@pgpool-01 pgpool-II]# pcp_watchdog_info -h 192.168.0.6 -p 9898 -U pgpool
Password: 
2 YES 192.168.0.3:9999 Linux pgpool-02 192.168.0.3

192.168.0.3:9999 Linux pgpool-02 192.168.0.3 9999 9000 4 MASTER
192.168.0.2:9999 Linux pgpool-01 192.168.0.2 9999 9000 7 STANDBY
[root@pgpool-01 pgpool-II]# psql -h 192.168.0.6 -p 9999 -U pgpool postgres -c "show pool_nodes"
Password for user pgpool: 
 node_id |   hostname   | port | status | lb_weight |  role   | select_cnt | load_balance_node | replication_delay | replication_state | re
plication_sync_state | last_status_change  
---------+--------------+------+--------+-----------+---------+------------+-------------------+-------------------+-------------------+---
---------------------+---------------------
 0       | postgresql01 | 5432 | up     | 0.500000  | standby | 0          | false             | 0                 |                   |   
                     | 2020-12-22 12:03:54
 1       | postgresql02 | 5432 | up     | 0.500000  | primary | 0          | true              | 0                 |                   |   
                     | 2020-12-22 12:03:54
(2 rows)

[root@pgpool-01 pgpool-II]# 
```



### Failover

First, use `psql` to connect to PostgreSQL via virtual IP, and verify the backend information.

```bash
[root@pgpool-01 pgpool-II]# psql -h 192.168.0.6 -p 9999 -U pgpool postgres -c "show pool_nodes"
Password for user pgpool: 
 node_id |   hostname   | port | status | lb_weight |  role   | select_cnt | load_balance_node | replication_delay | replication_state | re
plication_sync_state | last_status_change  
---------+--------------+------+--------+-----------+---------+------------+-------------------+-------------------+-------------------+---
---------------------+---------------------
 0       | postgresql01 | 5432 | up     | 0.500000  | standby | 0          | false             | 0                 |                   |   
                     | 2020-12-22 12:03:54
 1       | postgresql02 | 5432 | up     | 0.500000  | primary | 1          | true              | 0                 |                   |   
                     | 2020-12-22 12:03:54
(2 rows)

[root@pgpool-01 pgpool-II]# 
```

Next, stop primary PostgreSQL server `postgresql02`, and verify automatic failover.

```bash
[root@postgresql02 data]# systemctl stop postgresql-12
```

After stopping PostgreSQL on `postgresql02`, failover occurs and PostgreSQL on `postgresql01` becomes new primary DB.

```bash
[root@postgresql02 data]# psql -h 192.168.0.6 -p 9999 -U pgpool postgres -c "show pool_nodes"
Password for user pgpool: 
 node_id |   hostname   | port | status | lb_weight |  role   | select_cnt | load_balance_node | replication_delay | replication_state | re
plication_sync_state | last_status_change  
---------+--------------+------+--------+-----------+---------+------------+-------------------+-------------------+-------------------+---
---------------------+---------------------
 0       | postgresql01 | 5432 | up     | 0.500000  | primary | 0          | true              | 0                 |                   |   
                     | 2020-12-22 12:19:17
 1       | postgresql02 | 5432 | down   | 0.500000  | standby | 1          | false             | 0                 |                   |   
                     | 2020-12-22 12:19:17
(2 rows)

[root@postgresql02 data]# 
```

`postgresql01` is running as new primary.

```bash
[root@postgresql02 data]# psql -h postgresql01 -p 5432 -U pgpool postgres -c "select pg_is_in_recovery()"
Password for user pgpool: 
 pg_is_in_recovery 
-------------------
 f
(1 row)

[root@postgresql02 data]# 
```



### Online Recovery

```
[root@pgpool-01 pgpool-II]# psql -h 192.168.0.6 -p 9999 -U pgpool postgres -c "show pool_nodes"
Password for user pgpool: 
 node_id |   hostname   | port | status | lb_weight |  role   | select_cnt | load_balance_node | replication_delay | replication_s
tate | replication_sync_state | last_status_change  
---------+--------------+------+--------+-----------+---------+------------+-------------------+-------------------+--------------
-----+------------------------+---------------------
 0       | postgresql01 | 5432 | up     | 0.500000  | primary | 0          | true              | 0                 |              
     |                        | 2020-12-22 12:19:17
 1       | postgresql02 | 5432 | down   | 0.500000  | standby | 1          | false             | 0                 |              
     |                        | 2020-12-22 12:19:17
(2 rows)

[root@pgpool-01 pgpool-II]# 
```

**Next, run the command to execute script `recovery_1st_stage` and then `pgpool_remote_start`.**

```
pcp_recovery_node -h 192.168.0.6 -p 9898 -U pgpool -n 1
```

We can use command `tail -f /var/log/postgres_shell.log` on `postgresql01` to watch the process.



## 数据操作命令
### 1. 创建数据库和表并插入数据
```bash
postgres=# create database test;
postgres=# \c test
test=# create table tt(id serial not null,name text);
test=# insert into tt(name) values ('china');
test=# \q
```

### 2. postgresql用户创建相关
```bash
sudo -u postgres psql postgres
psql -U username -d database_name -h host -p port -W   # 指定参数登录
# 参数含义: -U指定用户 -d要连接的数据库 -h要连接的主机 -W提示输入密码。

create user dbuser    # 创建用户
create database exampledb owner dbuser    # 创建数据库
```



## Simulate postgresql recovery process

postgresql_02 is master, ip 192.168.0.5/24

postgresql_01 is slave, ip 192.168.0.4/24

First, stop master:

```
systemctl stop postgresql-12
```

Execute command at slave(postgresql_01), and promote standby node:

```bash
postgres=# export PGHOME=/usr/pgsql-12
postgres=# export NEW_MASTER_NODE_PGDATA=/var/lib/pgsql/12/data
postgres=# ${PGHOME}/bin/pg_ctl -D ${NEW_MASTER_NODE_PGDATA} -w promote
```

Check the standby node has change master:

```bash
# /usr/pgsql-12/bin/pg_controldata -D /var/lib/pgsql/12/data/ | grep 'Database cluster state'
Database cluster state:               in production
```




## 模拟 follow master 过程

```bash
# Check the status of Standby
export PGHOME=/usr/pgsql-12
export FAILED_NODE_PGDATA=/var/lib/pgsql/12/data
${PGHOME}/bin/pg_ctl -w -D ${FAILED_NODE_PGDATA} status

# If Standby is alive, stop it
${PGHOME}/bin/pg_ctl -w -m f -D ${FAILED_NODE_PGDATA} stop

# touch standby.signal
touch ${FAILED_NODE_PGDATA}/standby.signal

# 运行 pg_rewind 命令
export NEW_MASTER_NODE_HOST=192.168.0.4
export NEW_MASTER_NODE_PORT=5432
export NEW_MASTER_NODE_PASSWORD=postgres
${PGHOME}/bin/pg_rewind -D ${FAILED_NODE_PGDATA} --source-server="user=postgres host=${NEW_MASTER_NODE_HOST} port=${NEW_MASTER_NODE_PORT} password=${NEW_MASTER_NODE_PASSWORD}"

```



## 待整理

```



psql -U postgres -d postgres -h 192.168.0.2 -p 9999 -W
psql -U postgres -d postgres -h 192.168.0.5 -p 5432 -W

pg_basebackup -h 192.168.0.4 -U repl -p 5432 -D /var/lib/postgresql/12/main/  -Fp -Xs -P -R
pg_basebackup -h 192.168.0.4 -U repl -p 5432 -D /var/lib/pgsql/12/backups   -Fp -Xs -P -R

pg_basebackup -h 192.168.0.4 -U repl -p 5432 -D $PGDATA -Fp -Xs -P -R
pg_basebackup -h 192.168.0.5 -U repl -p 5432 -D $PGDATA -Fp -Xs -P -R

PGDATA=/usr/local/var/postgres


mkdir .ssh
cd .ssh/
ssh-keygen -t rsa -f id_rsa_pgpool

touch authorized_keys


ssh postgres@192.168.0.5 -i ~/.ssh/id_rsa_pgpool
ssh postgres@192.168.0.2 -i ~/.ssh/id_rsa_pgpool
ssh postgres@192.168.0.3 -i ~/.ssh/id_rsa_pgpool
ssh postgres@192.168.0.4 -i ~/.ssh/id_rsa_pgpool


scp -i ~/.ssh/id_rsa_pgpool /etc/pgpool-II/recovery_1st_stage.sample postgres@192.168.0.4:/var/lib/pgsql/12/data/recovery_1st_stage
scp -i ~/.ssh/id_rsa_pgpool /etc/pgpool-II/pgpool_remote_start.sample postgres@192.168.0.4:/var/lib/pgsql/12/data/pgpool_remote_start

chown postgres:postgres /var/lib/pgsql/12/data/{recovery_1st_stage,pgpool_remote_start}



Promote Standby node.
${PGHOME}/bin/pg_ctl -D ${NEW_MASTER_NODE_PGDATA} -w promote
/usr/pgsql-12/bin/pg_ctl -D /var/lib/pgsql/12/data -w promote



/usr/pgsql-12/bin/pg_rewind -D /var/lib/pgsql/12/data --source-server="user=postgres host=192.168.0.5 port=5432 password=postgres"
/usr/pgsql-12/bin/pg_rewind -D /var/lib/pgsql/12/data --source-server="user=postgres host=192.168.0.4 port=5432 password=postgres"


SELECT pg_create_physical_replication_slot('xxxxx');
SELECT pg_create_physical_replication_slot('xxxxx');


primary_conninfo = 'host=192.168.0.5 port=5432 user=repl'
application_name = '192.168.0.4'
passfile = '/var/lib/pgsql/.pgpass'
recovery_target_timeline = 'latest'
restore_command = 'scp 192.168.0.5:/var/lib/pgsql/archivedir/%f %p'
primary_slot_name = 'postgresql_01'



primary_conninfo = 'user=replicator password=1qaz2wsx host=192.168.56.101 port=5432 application_name=pg96_103'
standby_mode = 'on'
recovery_target_timeline = 'latest'
trigger_file = '/tmp/postgresql.trigger.5432'
primary_slot_name = 'pg96_101'




RECOVERYCONF=${FAILED_NODE_PGDATA}/myrecovery.conf
export PGHOME=/usr/pgsql-12
export FAILED_NODE_PGDATA=/var/lib/pgsql/12/data
export RECOVERYCONF=${FAILED_NODE_PGDATA}/myrecovery.conf
export FAILED_NODE_HOST=192.168.0.4
export NEW_MASTER_NODE_PORT=5432


${PGHOME}/bin/pg_ctl -w -D ${FAILED_NODE_PGDATA} status
${PGHOME}/bin/psql -p ${NEW_MASTER_NODE_PORT} -c \"SELECT pg_create_physical_replication_slot('${FAILED_NODE_HOST}');\"



psql -U postgres -d postgres -h 192.168.0.2 -p 9999 -W
psql -U postgres -d postgres -h 192.168.0.6 -p 5432 -W


if_up_cmd = /usr/bin/sudo /sbin/ip addr add 192.168.0.6/24 dev eth0 label eth0:0
if_down_cmd = /usr/bin/sudo /sbin/ip addr del 192.168.0.6/24 dev eth0
arping_cmd = /usr/bin/sudo /usr/sbin/arping -U 192.168.0.6 -w 1 -I eth0


psql -p 5432 "SELECT pg_drop_replication_slot('postgresql01');"
select * from pg_replication_slots;
SELECT pg_create_physical_replication_slot('postgresql02');
SELECT * FROM pg_drop_replication_slot('postgresql02');
insert into tt(name) values ('xxx12312xxx');



pcp_recovery_node -h 192.168.0.6 -p 9898 -U pgpool -n 1 -d -v


/usr/pgsql-12/bin/pg_ctl -l /dev/null -w -D /var/lib/pgsql/12/data start
/usr/pgsql-12/bin/pg_ctl -l /dev/null -w -D /var/lib/pgsql/12/data start


psql -h 192.168.0.6 -p 9999 -U pgpool postgres -c "show pool_nodes"


scp -i ~/.ssh/id_rsa_pgpool /etc/pgpool-II/pgpool.conf postgres@192.168.0.3:/etc/pgpool-II/pgpool.conf
scp -i ~/.ssh/id_rsa_pgpool /etc/pgpool-II/pcp.conf postgres@192.168.0.3:/etc/pgpool-II/pcp.conf
scp -i ~/.ssh/id_rsa_pgpool /etc/pgpool-II/recovery_1st_stage.sample postgres@192.168.0.4:/var/lib/pgsql/12/data/recovery_1st_stage
scp -i ~/.ssh/id_rsa_pgpool /etc/pgpool-II/pgpool_remote_start.sample postgres@192.168.0.4:/var/lib/pgsql/12/data/pgpool_remote_start
scp -i ~/.ssh/id_rsa_pgpool  /etc/pgpool-II/pgpool_remote_start.sample postgres@postgresql01:/var/lib/pgsql/12/data/pgpool_remote_start


tail -f /var/log/postgres_shell.log


recovery.signal: tells PostgreSQL to enter normal archive recovery
standby.signal: tells PostgreSQL to enter standby mode




```





## 参考

[Watchdog01](http://tatsuo-ishii.github.io/pgpool-II/current/example-watchdog.html)
[Watchdog02](http://tatsuo-ishii.github.io/pgpool-II/current/runtime-watchdog-config.html)
[RECOVERY.CONF IS GONE IN POSTGRESQL V12](https://www.cybertec-postgresql.com/en/recovery-conf-is-gone-in-postgresql-v12/)
[PostgreSQL master-slave database replication](https://medium.com/omis-engineering/django-multiple-database-system-using-postgresql-master-slave-database-architecture-f71d4e9e53ba)
[pg_rewind](https://blog.csdn.net/weixin_34082789/article/details/89692844)
[PostgreSQL流复制热备](https://www.jianshu.com/p/3d2cb1db6c2f)
[PGPool-II+PG流复制实现HA](https://www.jianshu.com/p/dab25c3af916?utm_campaign=haruki)
[linux日志logger命令详解](https://blog.csdn.net/u011630575/article/details/52055116)
[postgresql replication slots 的一些个人理解](https://blog.csdn.net/ctypyb2002/article/details/81207837)
[PostgreSQL常用命令01](https://blog.csdn.net/u010856284/article/details/70142810)
[PostgreSQL常用命令02](https://www.cnblogs.com/zhoujie/p/pgsql.html)