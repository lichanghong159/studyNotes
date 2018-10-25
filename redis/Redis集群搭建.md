# 环境说明

在一台机器上搭建

本次搭建的集群为3*(1主2从)共9个节点。

端口分别为:7000,7001,7002,7003,7004,7005,7006,7007,7008

# 修改配置文件

## 创建集群文件夹

>  mkdir /opt/cluster-test

> cd /opt/cluster-test
>
> mkdir 7000 7001 7002 7003 7004 7005 7006 7007 7008

## 修改配置文件

修改/opt/redis-3.2.12/redis.conf配置文件

> vim  /opt/redis-3.2.12/redis.conf

```properties
#注释掉 
#bind 127.0.0.1
#将端口修改为7000
port 7000
#protected-mode改为no
protected-mode no
#修改pidfile
pidfile /var/run/redis-7000.pid  
#修改dbfilename
dbfilename dump-7000.rdb 
#修改appendfilename
appendfilename "appendonly-7000.aof"  
#修改cluster-config-file
cluster-config-file nodes-7000.conf  
#修改cluster-enabled
cluster-enabled yes  
#cluster-node-timeout
cluster-node-timeout 5000  
#appendonly
appendonly yes  
```

> 将redis.conf配置文件拷贝到/opt/cluster-test/7000
>
> cp /opt/redis-3.2.12/redis.conf /opt/cluster-test/7000

将redis.conf文件分别拷贝到7001-7008，并修改相应的配置

# **安装ruby,rubygems**

```
# yum -y install gcc openssl-devel libyaml-devel libffi-devel readline-devel zlib-devel gdbm-devel ncurses-devel gcc-c++ automake autoconf  
  
# yum -y install ruby rubygems   //安装ruby rubygems  
  
//换源  
# gem source -l  
# gem source --remove http://rubygems.org/  
# gem sources -a http://ruby.taobao.org/  
# gem source -l  
  
# gem install redis --version 3.0.0  //安装gem_redis  
Successfully installed redis-3.0.0  
1 gem installed  
Installing ri documentation for redis-3.0.0...  
Installing RDoc documentation for redis-3.0.0...  
```

# 编写启动脚本

> vim /opt/cluster-test/start.sh
>
> redis-server /opt/cluster-test/7000/redis.conf
> redis-server /opt/cluster-test/7001/redis.conf
> redis-server /opt/cluster-test/7002/redis.conf
> redis-server /opt/cluster-test/7003/redis.conf
> redis-server /opt/cluster-test/7004/redis.conf
> redis-server /opt/cluster-test/7005/redis.conf
> redis-server /opt/cluster-test/7006/redis.conf
> redis-server /opt/cluster-test/7007/redis.conf
> redis-server /opt/cluster-test/7008/redis.conf

给脚本赋权限

> chmod 755 /opt/cluster-test/start.sh

# 启动redis-server

> /opt/cluster-test/start.sh

查看redis启动情况

> ps -ef |grep redis

输出:

> root       4589      1  0 10:56 ?        00:00:00 redis-server *:7000 [cluster]
> root       4593      1  0 10:56 ?        00:00:00 redis-server *:7001 [cluster]
> root       4595      1  0 10:56 ?        00:00:00 redis-server *:7002 [cluster]
> root       4597      1  0 10:56 ?        00:00:00 redis-server *:7003 [cluster]
> root       4599      1  0 10:56 ?        00:00:00 redis-server *:7004 [cluster]
> root       4601      1  0 10:56 ?        00:00:00 redis-server *:7005 [cluster]
> root       4613      1  0 10:56 ?        00:00:00 redis-server *:7006 [cluster]
> root       4615      1  0 10:56 ?        00:00:00 redis-server *:7007 [cluster]
> root       4619      1  0 10:56 ?        00:00:00 redis-server *:7008 [cluster]

# **创建集群，并查看**

## **创建redis集群**

> redis-trib.rb create --replicas 2 192.168.170.80:7000 192.168.170.80:7001 192.168.170.80:7002 192.168.170.80:7003 192.168.170.80:7004 192.168.170.80:7005 192.168.170.80:7006 192.168.170.80:7007 192.168.170.80:7008

输出：

> Creating cluster
> Performing hash slots allocation on 9 nodes...
> Using 3 masters:
> 192.168.170.80:7000
> 192.168.170.80:7001
> 192.168.170.80:7002
> Adding replica 192.168.170.80:7003 to 192.168.170.80:7000
> Adding replica 192.168.170.80:7004 to 192.168.170.80:7000
> Adding replica 192.168.170.80:7005 to 192.168.170.80:7001
> Adding replica 192.168.170.80:7006 to 192.168.170.80:7001
> Adding replica 192.168.170.80:7007 to 192.168.170.80:7002
> Adding replica 192.168.170.80:7008 to 192.168.170.80:7002
> M: d77e442366448ec389910ff7ac04dbdd984c5e47 192.168.170.80:7000
> slots:0-5460 (5461 slots) master
> M: c80874872120c3d6a1323ced9fe635e3b56744e0 192.168.170.80:7001
> slots:5461-10922 (5462 slots) master
> M: 144054aa7221afb4a4a284122f4a6573e64d2a17 192.168.170.80:7002
> slots:10923-16383 (5461 slots) master
> S: 03edf19d19db105a691d4bec01555962438d0769 192.168.170.80:7003
> replicates d77e442366448ec389910ff7ac04dbdd984c5e47
> S: 4558a0e33f6bf6c56517fb2aa6186836901d5af2 192.168.170.80:7004
> replicates d77e442366448ec389910ff7ac04dbdd984c5e47
> S: 3dc9ce1da7bf50982ad6536d5312b58fae1641d9 192.168.170.80:7005
> replicates c80874872120c3d6a1323ced9fe635e3b56744e0
> S: d1bf804a6396c39b9c5a97153a4e45ab7c3e5300 192.168.170.80:7006
> replicates c80874872120c3d6a1323ced9fe635e3b56744e0
> S: 58c43764e2de3d15b26cfa73f8845c191d7b63fe 192.168.170.80:7007
> replicates 144054aa7221afb4a4a284122f4a6573e64d2a17
> S: dcd415ba314e008c89de96e7596f6625139b4649 192.168.170.80:7008
> replicates 144054aa7221afb4a4a284122f4a6573e64d2a17
> Can I set the above configuration? (type 'yes' to accept):

输入：`yes`

>Nodes configuration updated
>Assign a different config epoch to each node
>Sending CLUSTER MEET messages to join the cluster
>Waiting for the cluster to join...
>Performing Cluster Check (using node 192.168.170.80:7000)
>M: d77e442366448ec389910ff7ac04dbdd984c5e47 192.168.170.80:7000
>slots:0-5460 (5461 slots) master
>2 additional replica(s)
>S: 58c43764e2de3d15b26cfa73f8845c191d7b63fe 192.168.170.80:7007
>slots: (0 slots) slave
>replicates 144054aa7221afb4a4a284122f4a6573e64d2a17
>S: dcd415ba314e008c89de96e7596f6625139b4649 192.168.170.80:7008
>slots: (0 slots) slave
>replicates 144054aa7221afb4a4a284122f4a6573e64d2a17
>S: 03edf19d19db105a691d4bec01555962438d0769 192.168.170.80:7003
>slots: (0 slots) slave
>replicates d77e442366448ec389910ff7ac04dbdd984c5e47
>S: 3dc9ce1da7bf50982ad6536d5312b58fae1641d9 192.168.170.80:7005
>slots: (0 slots) slave
>replicates c80874872120c3d6a1323ced9fe635e3b56744e0
>M: 144054aa7221afb4a4a284122f4a6573e64d2a17 192.168.170.80:7002
>slots:10923-16383 (5461 slots) master
>2 additional replica(s)
>S: 4558a0e33f6bf6c56517fb2aa6186836901d5af2 192.168.170.80:7004
>slots: (0 slots) slave
>replicates d77e442366448ec389910ff7ac04dbdd984c5e47
>S: d1bf804a6396c39b9c5a97153a4e45ab7c3e5300 192.168.170.80:7006
>slots: (0 slots) slave
>replicates c80874872120c3d6a1323ced9fe635e3b56744e0
>M: c80874872120c3d6a1323ced9fe635e3b56744e0 192.168.170.80:7001
>slots:5461-10922 (5462 slots) master
>2 additional replica(s)
>[OK] All nodes agree about slots configuration.
>Check for open slots...
>Check slots coverage...
>[OK] All 16384 slots covered.

集群创建完成

## 查看集群

> redis-trib.rb  check 192.168.170.80:7000

输出:

>Performing Cluster Check (using node 192.168.170.80:7000)
>M: d77e442366448ec389910ff7ac04dbdd984c5e47 192.168.170.80:7000
>slots:0-5460 (5461 slots) master
>2 additional replica(s)
>S: 58c43764e2de3d15b26cfa73f8845c191d7b63fe 192.168.170.80:7007
>slots: (0 slots) slave
>replicates 144054aa7221afb4a4a284122f4a6573e64d2a17
>S: dcd415ba314e008c89de96e7596f6625139b4649 192.168.170.80:7008
>slots: (0 slots) slave
>replicates 144054aa7221afb4a4a284122f4a6573e64d2a17
>S: 03edf19d19db105a691d4bec01555962438d0769 192.168.170.80:7003
>slots: (0 slots) slave
>replicates d77e442366448ec389910ff7ac04dbdd984c5e47
>S: 3dc9ce1da7bf50982ad6536d5312b58fae1641d9 192.168.170.80:7005
>slots: (0 slots) slave
>replicates c80874872120c3d6a1323ced9fe635e3b56744e0
>M: 144054aa7221afb4a4a284122f4a6573e64d2a17 192.168.170.80:7002
>slots:10923-16383 (5461 slots) master
>2 additional replica(s)
>S: 4558a0e33f6bf6c56517fb2aa6186836901d5af2 192.168.170.80:7004
>slots: (0 slots) slave
>replicates d77e442366448ec389910ff7ac04dbdd984c5e47
>S: d1bf804a6396c39b9c5a97153a4e45ab7c3e5300 192.168.170.80:7006
>slots: (0 slots) slave
>replicates c80874872120c3d6a1323ced9fe635e3b56744e0
>M: c80874872120c3d6a1323ced9fe635e3b56744e0 192.168.170.80:7001
>slots:5461-10922 (5462 slots) master
>2 additional replica(s)
>[OK] All nodes agree about slots configuration.
>Check for open slots...
>Check slots coverage...
>[OK] All 16384 slots covered.

## 命令说明

- 给定 `redis-trib.rb` 程序的命令是 `create` ， 这表示我们希望创建一个新的集群。
- 选项 `--replicas 2` 表示我们希望为集群中的每个主节点创建2个从节点。
- 之后跟着的其他参数则是实例的地址列表， 我们希望程序使用这些地址所指示的实例来创建新集群。

简单来说， 以上命令的意思就是让 `redis-trib` 程序创建一个包含3个主节点和6个从节点的集群。



