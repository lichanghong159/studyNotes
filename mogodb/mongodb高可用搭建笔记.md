### mongodb高可用搭建笔记

#### 创建3台数据副本集

创建3台数据副本集shardsvr1、shardsvr2、shardsvr3,端口分别对应27001、27002、27003,replSet设置为replSet01:

1.关闭防火墙

```html
systemctl stop firewalld.service
```

##### 创建目录

```ht
 mkdir mymongo
 mkdir -p mymongo/shardsvr/shardsvr1/data
 mkdir -p mymongo/shardsvr/shardsvr1/log
 mkdir -p mymongo/shardsvr/shardsvr2/data
 mkdir -p mymongo/shardsvr/shardsvr2/log
 mkdir -p mymongo/shardsvr/shardsvr3/data
 mkdir -p mymongo/shardsvr/shardsvr3/log
```

##### 创建mongodb.cfg

- **shardsvr1**

```ht
[root@localhost shardsvr1]# vi mongodb.cfg
```

```ht
dbpath=/usr/local/DATA/mymongo/shardsvr/shardsvr1/data
logpath=/usr/local/DATA/mymongo/shardsvr/shardsvr1/log/mongodb.log
logappend=true
fork=true
bind_ip=0.0.0.0
port=27001
replSet=replSet01
shardsvr=true
```

- **shardsvr2**

 ```ht
[root@localhost shardsvr2]# vi mongodb.cfg
 ```

```html
dbpath=/usr/local/DATA/mymongo/shardsvr/shardsvr2/data
logpath=/usr/local/DATA/mymongo/shardsvr/shardsvr2/log/mongodb.log
logappend=true
fork=true
bind_ip=0.0.0.0
port=27002
replSet=replSet01
shardsvr=true
```

- **shardsvr3**

```html
[root@localhost shardsvr3]# vi mongodb.cfg
```

```html
dbpath=/usr/local/DATA/mymongo/shardsvr/shardsvr3/data
logpath=/usr/local/DATA/mymongo/shardsvr/shardsvr3/log/mongodb.log
logappend=true
fork=true
bind_ip=0.0.0.0
port=27003
replSet=replSet01
shardsvr=true
```

- **启动3个服务**

```ht
[root@localhost shardsvr1]# mongod -f mongodb.cfg
[root@localhost shardsvr2]# mongod -f mongodb.cfg
[root@localhost shardsvr3]# mongod -f mongodb.cfg
```

  1.**查看运行状态**

  ```ht
netstat -anp | grep mongod
ps -aux | grep mongod
  ```

- **使数据节点生效**

```html
[root@localhost shardsvr3]# mongo 127.0.0.1:27001
use admin
cfg={_id:"replSet01",members:[
{_id:0,host:'127.0.0.1:27001'},
{_id:1,host:'127.0.0.1:27002'},
{_id:2,host:'127.0.0.1:27003'}]};
rs.initiate(cfg);
```

2.创建3台配置副本集(28001/28002/28003-configsvrs)

mkdir -p mymongo/configsvr/configsvr1/data

mkdir -p mymongo/configsvr/configsvr1/log

mkdir -p mymongo/configsvr/configsvr2/data

mkdir -p mymongo/configsvr/configsvr2/log

mkdir -p mymongo/configsvr/configsvr3/data

mkdir -p mymongo/configsvr/configsvr3/log

3.配置1台路由服务器(30000)

mkdir -p mymongo/routesvr/log



