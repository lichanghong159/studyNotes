# centos7安装MySQL、redis

环境说明：

* OS :centos7(1511)x86_x64
* mysql5.7+
* redis4.0+

## 更新163镜像源

### 备份配置文件

`mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup`

### 下载163的配置文件

`cd /etc/yum.repos.d/`

`wget http://mirrors.163.com/.help/CentOS7-Base-163.repo`

`mv CentOS7-Base-163.repo CentOS7-Base.repo `

### 更新镜像源

`yum clean all`

`yum makecache `

## 安装mysql

我是通过网络直接安装，如果是本地安装包，请自行百度教程,如果之前安装过mysql5.7一下的版本，请自行卸载。

### 下载Mysql rpm

`wget -i -c http://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm`

`yum -y install mysql57-community-release-el7-10.noarch.rpm`

`yum -y install mysql-community-server`

### 启动mysql

`systemctl start  mysqld.service`

### 设置开机启动

`systemctl enable  mysqld.service`

### 修改root密码

通过`grep "password" /var/log/mysqld.log`查找root初始密码。

`mysql -uroot -p`登录后，自行修改即可

### 开放远程端口

`firewall-cmd --zone=public --add-port=3306/tcp --permanent`

`systemctl status mysqld.service`

==不建议之间关闭防火墙==

## 安装Redis

### 上传源码

将源码包上传至服务器

Redis编译环境

Redis编译环境

 ### Redis编译环境

`yum install gcc-c++ -y`

### 编译源码

#### 解压源码包

`tar -xzvf redis-4.0.11.tar.gz `

#### 进入Redis源码目录

`cd redis-4.0.11`

#### 编译Redis

`make MALLOC=libc`

编译完成后，会在src下产生redis-server、redis-cli、redis-benchmark、redis-check-aof、redis-check-rdb、redis-sentinel共6个可执行的文件

### 安装Redis

#### 进入src目录下

`cd src/`

#### 安装

`make isntall`



### Redis开机自启

#### 进入Redis的utils目录

`cd /usr/local/redis-4.0.11/urils`

#### 执行命令

`./install_server.sh`

如果不需要更改配置，就一路回车即可

#### 查看进程

`ps -ef |grep redis`

### 设置远程访问

#### 修改配置文件

`vim /etc/redis/6379.conf`

修改一下几项即可

* daemonize 改成yes `daemonize yes`
* bind 127.0.0.1 注释掉 #bind 127.0.0.1
* protected-mode 改成 no `protected-mode no`

#### 重新redis

`service redis_6379 restart`

### 开放端口

firewall-cmd --zone=public --add-port=6379/tcp --permanent

### 重新防火墙

systemctl restart firewalld.service 