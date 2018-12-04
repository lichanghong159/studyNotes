# 修改my.cnf

## MasterA配置文件

```shell
[client]
port = 3306
socket = /tmp/mysql.sock

[mysqld]
basedir = /opt/mysql57
port = 3306
socket = /tmp/mysql.sock
datadir = /opt/mysql57/data
log-error = /opt/mysql57/data/mysql.err

server-id = 1
auto_increment_offset = 1
auto_increment_increment = 2                                           #偶数ID

log-bin = mysql-bin                                                     #打开二进制功能,MASTER主服务器必须打开此项
binlog-format=ROW
log-slave-updates=true
gtid-mode=on
enforce-gtid-consistency=true
master-info-repository=TABLE
relay-log-info-repository=TABLE
sync-master-info=1
slave-parallel-workers=0
sync_binlog=0
binlog-checksum=CRC32
master-verify-checksum=1
slave-sql-verify-checksum=1
binlog-rows-query-log_events=1
#expire_logs_days=5
max_binlog_size=1024M                                                   #binlog单文件最大值

replicate-ignore-db = mysql                                             #忽略不同步主从的数据库
replicate-ignore-db = information_schema
replicate-ignore-db = performance_schema

max_connections = 3000
max_connect_errors = 30

skip-character-set-client-handshake                                     #忽略应用程序想要设置的其他字符集
init-connect='SET NAMES utf8'                                           #连接时执行的SQL
character-set-server=utf8                                               #服务端默认字符集
wait_timeout=1800                                                       #请求的最大连接时间
interactive_timeout=1800                                                #和上一参数同时修改才会生效
sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES                     #sql模式
max_allowed_packet = 10M
bulk_insert_buffer_size = 8M
query_cache_type = 1
query_cache_size = 128M
query_cache_limit = 4M
key_buffer_size = 256M
read_buffer_size = 16K

skip-name-resolve
slow_query_log=1
long_query_time = 6
slow_query_log_file=slow-query.log
innodb_flush_log_at_trx_commit = 2
innodb_log_buffer_size = 16M

[mysql]
no-auto-rehash

[myisamchk]
key_buffer_size = 20M
sort_buffer_size = 20M
read_buffer = 2M
write_buffer = 2M

[mysqlhotcopy]
interactive-timeout

[mysqldump]
quick
max_allowed_packet = 16M

[mysqld_safe]
```

## MasterB的配置文件

```shell
[client]
port = 3306
socket = /tmp/mysql.sock

[mysqld]
basedir = /opt/mysql57
port = 3306
socket = /tmp/mysql.sock
datadir = /opt/mysql57/data
log-error = /opt/mysql57/data/mysql.err

server-id = 2
auto_increment_offset = 2
auto_increment_increment = 2                                           #偶数ID

log-bin = mysql-bin                                                     #打开二进制功能,MASTER主服务器必须打开此项
binlog-format=ROW
log-slave-updates=true
gtid-mode=on
enforce-gtid-consistency=true
master-info-repository=TABLE
relay-log-info-repository=TABLE
sync-master-info=1
slave-parallel-workers=0
sync_binlog=0
binlog-checksum=CRC32
master-verify-checksum=1
slave-sql-verify-checksum=1
binlog-rows-query-log_events=1
#expire_logs_days=5
max_binlog_size=1024M                                                   #binlog单文件最大值

replicate-ignore-db = mysql                                             #忽略不同步主从的数据库
replicate-ignore-db = information_schema
replicate-ignore-db = performance_schema

max_connections = 3000
max_connect_errors = 30

skip-character-set-client-handshake                                     #忽略应用程序想要设置的其他字符集
init-connect='SET NAMES utf8'                                           #连接时执行的SQL
character-set-server=utf8                                               #服务端默认字符集
wait_timeout=1800                                                       #请求的最大连接时间
interactive_timeout=1800                                                #和上一参数同时修改才会生效
sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES                     #sql模式
max_allowed_packet = 10M
bulk_insert_buffer_size = 8M
query_cache_type = 1
query_cache_size = 128M
query_cache_limit = 4M
key_buffer_size = 256M
read_buffer_size = 16K

skip-name-resolve
slow_query_log=1
long_query_time = 6
slow_query_log_file=slow-query.log
innodb_flush_log_at_trx_commit = 2
innodb_log_buffer_size = 16M

[mysql]
no-auto-rehash

[myisamchk]
key_buffer_size = 20M
sort_buffer_size = 20M
read_buffer = 2M
write_buffer = 2M

[mysqlhotcopy]
interactive-timeout

[mysqldump]
quick
max_allowed_packet = 16M

[mysqld_safe]
```

# 配置主从同步

## 添加主从同步帐号

### masterA

```shell
mysql> grant replication slave on *.* to 'repl'@'192.168.170.101' identified by '123456';
mysql> flush privileges;
```



### masterB

```shell
mysql> grant replication slave on *.* to 'repl'@'192.168.170.100' identified by '123456';
mysql> flush privileges;
```



## 查看主库的状态

### masterA

```shell
mysql> show master status \G;
*************************** 1. row ***************************
             File: mysql-bin.000006
         Position: 1501
     Binlog_Do_DB: 
 Binlog_Ignore_DB: 
Executed_Gtid_Set: 2fe73988-d826-11e8-8ad8-000c29a7b21e:1-10
1 row in set (0.00 sec)
```



### masterB

```shell
mysql> show master status \G;
*************************** 1. row ***************************
             File: mysql-bin.000004
         Position: 1501
     Binlog_Do_DB: 
 Binlog_Ignore_DB: 
Executed_Gtid_Set: 38665fdf-d826-11e8-9bdb-000c2993b04c:1-8
1 row in set (0.00 sec)
```

## 配置同步信息

### masterA

```shell
mysql> change master to master_host='192.168.170.101',master_port=3306,master_user='repl',master_password='123456',master_log_file='mysql-bin.000004',master_log_pos=1501;
mysql> start slave;
mysql> show slave status\G;
```

```shell
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: 192.168.170.101
                  Master_User: repl
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000004
          Read_Master_Log_Pos: 1501
               Relay_Log_File: localhost-relay-bin.000002
                Relay_Log_Pos: 320
        Relay_Master_Log_File: mysql-bin.000004
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes
              Replicate_Do_DB: 
          Replicate_Ignore_DB: mysql,information_schema,performance_schema
           Replicate_Do_Table: 
       Replicate_Ignore_Table: 
      Replicate_Wild_Do_Table: 
  Replicate_Wild_Ignore_Table: 
                   Last_Errno: 0
                   Last_Error: 
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 1501
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
                  Master_UUID: 38665fdf-d826-11e8-9bdb-000c2993b04c
             Master_Info_File: mysql.slave_master_info
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
            Executed_Gtid_Set: 2fe73988-d826-11e8-8ad8-000c29a7b21e:1-10
                Auto_Position: 0
         Replicate_Rewrite_DB: 
                 Channel_Name: 
           Master_TLS_Version: 
1 row in set (0.00 sec)
```



### masterB

```shell
mysql> change master to master_host='192.168.170.100',master_port=3306,master_user='repl',master_password='123456',master_log_file='mysql-bin.000006',master_log_pos=1501;
mysql> start slave;
mysql> show slave status\G;
```

```shell
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: 192.168.170.100
                  Master_User: repl
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000006
          Read_Master_Log_Pos: 1501
               Relay_Log_File: localhost-relay-bin.000002
                Relay_Log_Pos: 320
        Relay_Master_Log_File: mysql-bin.000006
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes
              Replicate_Do_DB: 
          Replicate_Ignore_DB: mysql,information_schema,performance_schema
           Replicate_Do_Table: 
       Replicate_Ignore_Table: 
      Replicate_Wild_Do_Table: 
  Replicate_Wild_Ignore_Table: 
                   Last_Errno: 0
                   Last_Error: 
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 1501
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
             Master_Server_Id: 1
                  Master_UUID: 2fe73988-d826-11e8-8ad8-000c29a7b21e
             Master_Info_File: mysql.slave_master_info
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
            Executed_Gtid_Set: 38665fdf-d826-11e8-9bdb-000c2993b04c:1-8
                Auto_Position: 0
         Replicate_Rewrite_DB: 
                 Channel_Name: 
           Master_TLS_Version: 
1 row in set (0.00 sec)

```

## 开启MySQL5.6的GTID功能

masterA和masterB分别执行如下命令：

```shell
mysql> stop slave;

Query OK, 0 rows affected (0.00 sec)

mysql> change master to MASTER_AUTO_POSITION=1;

Query OK, 0 rows affected (0.01 sec)

mysql> start slave;

Query OK, 0 rows affected (0.00 sec)
```

