# 环境准备

## 卸载系统自带的Mariadb

[root@hdp265dnsnfs ~]# rpm -qa|grep mariadb
mariadb-libs-5.5.44-2.el7.centos.x86_64
[root@hdp265dnsnfs ~]# rpm -e --nodeps mariadb-libs-5.5.44-2.el7.centos.x86_64

## 删除etc目录下的my.cnf文件

[root@hdp265dnsnfs ~]# rm /etc/my.cnf
rm: cannot remove ?etc/my.cnf? No such file or directory

## 检查mysql是否存在

[root@hdp265dnsnfs ~]# rpm -qa | grep mysql
[root@hdp265dnsnfs ~]# 

## 检查mysql组和用户是否存在，如无创建

[root@hdp265dnsnfs ~]# cat /etc/group | grep mysql 
[root@hdp265dnsnfs ~]#  cat /etc/passwd | grep mysql

## 创建mysql用户组

[root@hdp265dnsnfs ~]# groupadd mysql
#创建一个用户名为mysql的用户并加入mysql用户组
[root@hdp265dnsnfs ~]# useradd -g mysql mysql
## 制定password 为111111

[root@hdp265dnsnfs ~]# passwd mysql
Changing password for user mysql.
New password: 
BAD PASSWORD: The password is a palindrome
Retype new password: 
passwd: all authentication tokens updated successfully.

## 由于我的/usr/local空间不足，所以我安装到/var

[root@hdp265dnsnfs var]# tar -zxvf mysql-5.7.18-linux-glibc2.5-x86_64.tar.gz 
[root@hdp265dnsnfs var]# mv mysql-5.7.18-linux-glibc2.5-x86_64/ mysql57

#更改所属的组和用户
[root@hdp265dnsnfs var]# chown -R mysql mysql57/
[root@hdp265dnsnfs var]# chgrp -R mysql mysql57/
[root@hdp265dnsnfs var]# cd mysql57/

[root@hdp265dnsnfs mysql57]# mkdir data

[root@hdp265dnsnfs mysql57]# chown -R mysql:mysql data

# 修改配置文件

在etc下新建配置文件my.cnf，并在该文件内添加以下配置

```
[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8 
[mysqld]
skip-name-resolve
#设置3306端口
port = 3306 
# 设置mysql的安装目录
basedir=/var/mysql57
# 设置mysql数据库的数据的存放目录
datadir=/var/mysql57/data
# 允许最大连接数
max_connections=200
# 服务端使用的字符集默认为8比特编码的latin1字符集
character-set-server=utf8
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB 
lower_case_table_names=1
max_allowed_packet=16M
```

# 安装和初始化

```shell
[root@hdp265dnsnfs mysql57]# bin/mysql_install_db --user=mysql --basedir=/var/mysql57/ --datadir=/var/mysql57/data/
2017-04-17 17:40:02 [WARNING] mysql_install_db is deprecated. Please consider switching to mysqld --initialize
2017-04-17 17:40:05 [WARNING] The bootstrap log isn't empty:
2017-04-17 17:40:05 [WARNING] 2017-04-17T09:40:02.728710Z 0 [Warning] --bootstrap is deprecated. Please consider using --initialize instead
2017-04-17T09:40:02.729161Z 0 [Warning] Changed limits: max_open_files: 1024 (requested 5000)
2017-04-17T09:40:02.729167Z 0 [Warning] Changed limits: table_open_cache: 407 (requested 2000)
```

```she
[root@hdp265dnsnfs mysql57]# cp ./support-files/mysql.server /etc/init.d/mysqld
[root@hdp265dnsnfs mysql57]# chown 777 /etc/my.cnf 
[root@hdp265dnsnfs mysql57]# chmod +x /etc/init.d/mysqld
```

## 设置开机启动

```she
[root@hdp265dnsnfs mysql57]# /etc/init.d/mysqld restart
Shutting down MySQL.. SUCCESS! 
Starting MySQL. SUCCESS! 

#设置开机启动

[root@hdp265dnsnfs mysql57]# chkconfig --level 35 mysqld on
[root@hdp265dnsnfs mysql57]# chkconfig --list mysqld

[root@hdp265dnsnfs mysql57]# chmod +x /etc/rc.d/init.d/mysqld
[root@hdp265dnsnfs mysql57]# chkconfig --add mysqld
[root@hdp265dnsnfs mysql57]# chkconfig --list mysqld
[root@hdp265dnsnfs mysql57]# service mysqld status
 SUCCESS! MySQL running (4475)
```

## 编辑etc/profile/

在文件末尾追加`export PATH=$PATH:/var/mysql57/bin`

## 让配置生效

```shel
[root@hdp265dnsnfs mysql57]# source /etc/profile
```

## 获得初始密码

```shell
[root@hdp265dnsnfs bin]# cat /root/.mysql_secret  
# Password set for user 'root@localhost' at 2017-04-17 17:40:02 
_pB*3VZl5T<6
```

## 修改密码

```shell
[root@hdp265dnsnfs bin]# mysql -uroot -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 5
Server version: 5.7.18

Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> set PASSWORD = PASSWORD('111111');
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql> flush privileges;
Query OK, 0 rows affected (0.01 sec)
```

## 设置远程访问



```shell
grant all privileges  on *.* to root@'%' identified by "root";
flush privileges;
```

