# 环境说明

## 服务器

服务器为centos7

### 主机A

IP:192.168.170.100

### 主机B

IP:192.168.170.101

## mysql

mysql版本为mysql-5.7.22-el7-x86_64

# 主从配置

## 配置master

### 修改my.cnf

```shell
[root@localhost ~]# vim /etc/my.cnf 
# 增加
[mysqld]
log-bin=mysql-bin
server-id=2
# 忽略的数据库
binlog-ignore-db=information_schema 
binlog-ignore-db=sys
binlog-ignore-db=mysql
# 同步的数据
binlog-do-db=db_user
binlog-do-db=db_store
```

### 重启mysql

```shell
/etc/init.d/mysqld restart
```

### 配置同步帐号

```shell
mysql，mysql -uroot -p

grant FILE on *.* to 'slave_user'@'192.168.170.101' identified by 'slave';
grant replication slave on *.* to 'slave_user'@'192.168.170.101' identified by 'slave';
flush privileges;
```

### 查看master信息

1、重启mysql

```shell
/etc/init.d/mysqld restart
```

2、登录mysql

```shell
mysql，mysql -uroot -p
mysql> show master status;
+------------------+----------+------------------+------------------------------+-------------------+
| File             | Position | Binlog_Do_DB     | Binlog_Ignore_DB             | Executed_Gtid_Set |
+------------------+----------+------------------+------------------------------+-------------------+
| mysql-bin.000002 |      154 | db_user,db_store | information_schema,sys,mysql |                   |
+------------------+----------+------------------+------------------------------+-------------------+

```

## 配置Slave

### 修改my.cnf

```shell
[root@localhost ~]# vim /etc/my.cnf 
# 增加
[mysqld]
# 主从配置
log-bin=mysql-bin
server-id=3
binlog-ignore-db=information_schema
binlog-ignore-db=sys
binlog-ignore-db=mysql
binlog-do-db=db_user
binlog-do-db=db_store
log-slave-updates
slave-skip-errors=all
slave-net-timeout=60 
```

### 重启mysql

```shell
/etc/init.d/mysqld restart
```

### 配置同步帐号

```shell
mysql，mysql -uroot -p

stop slave;
change master to master_host='192.168.170.100',master_user='slave_user',master_password='slave',master_log_file='mysql-bin.000002', master_log_pos=154;
start slave;
```

### 查看Slave信息

登录mysql

```shell
mysql，mysql -uroot -p
mysql> show slave status\G;
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: 192.168.170.100
                  Master_User: slave_user
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000002
          Read_Master_Log_Pos: 154
               Relay_Log_File: localhost-relay-bin.000002
                Relay_Log_Pos: 320
        Relay_Master_Log_File: mysql-bin.000002
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes
              Replicate_Do_DB: 
          Replicate_Ignore_DB: 
           Replicate_Do_Table: 
       Replicate_Ignore_Table: 
      Replicate_Wild_Do_Table: 
  Replicate_Wild_Ignore_Table: 
                   Last_Errno: 0
                   Last_Error: 
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 154
              Relay_Log_Space: 531
              Until_Condition: None
               Until_Log_File: 
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File: 
           Master_SSL_CA_Path: 
              Master_SSL_Cert: 
            Master_SSL_Cipher: 
               Master_SSL_Key: 
        Seconds_Behind_Master: 0
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error: 
               Last_SQL_Errno: 0
               Last_SQL_Error: 
  Replicate_Ignore_Server_Ids: 
             Master_Server_Id: 2
                  Master_UUID: f1621441-d5c6-11e8-8afb-000c29a7b21e
             Master_Info_File: /opt/mysql57/data/master.info
                    SQL_Delay: 0
          SQL_Remaining_Delay: NULL
      Slave_SQL_Running_State: Slave has read all relay log; waiting for more updates
           Master_Retry_Count: 86400
                  Master_Bind: 
      Last_IO_Error_Timestamp: 
     Last_SQL_Error_Timestamp: 
               Master_SSL_Crl: 
           Master_SSL_Crlpath: 
           Retrieved_Gtid_Set: 
            Executed_Gtid_Set: 
                Auto_Position: 0
         Replicate_Rewrite_DB: 
                 Channel_Name: 
           Master_TLS_Version: 
1 row in set (0.00 sec)

```



