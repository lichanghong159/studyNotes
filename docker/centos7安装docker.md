# 环境说明

* 服务器:centos7_1511
* IP地址:192.168.170.100

# 配置163源

## 关闭防火墙

> systemctl disable firewalld.service
>
> systemctl stop firewalld.service

## 配置163源

> mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup

[文档地址](http://mirrors.163.com/.help/centos.html)

> yum clean all
>
> yum makecache



# 安装docker

[文档地址](https://docs.docker.com/install/linux/docker-ce/centos/)

1、Docker 要求 CentOS 系统的内核版本高于 3.10 ，查看本页面的前提条件来验证你的CentOS 版本是否支持 Docker 。

通过 **uname -r** 命令查看你当前的内核版本

> ```shell
> uname -r
> ```

## 卸载旧版本

如果之前安装过旧版本的docker，通过下面的命令进行卸载

> ```shell
> sudo yum remove docker docker-client  docker-client-latest \
>                   docker-common docker-latest  docker-latest-logrotate \
>                   docker-logrotate  docker-selinux  docker-engine-selinux \
>                   docker-engine
> ```

## 安装需要的软件

安装所需的包。`yum-utils`提供了`yum-config-manager` 效用，并`device-mapper-persistent-data`和`lvm2`由需要 `devicemapper`存储驱动程序。

> ```shell
> sudo yum install -y yum-utils device-mapper-persistent-data  lvm2
> ```

## 添加docker源

> ```shell
> sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
> ```

## 安装DOCKER CE

### 安装最新版本的Docker CE，或转到下一步安装特定版本：

> ```
>  sudo yum install -y docker-ce
> ```

### 启动docker

> ```shell
> sudo systemctl start docker
> ```

> ps -ef |grep docker

日志输出：

> root      22686      1  1 15:12 ?        00:00:00 /usr/bin/dockerd
> root      22694  22686  0 15:12 ?        00:00:00 docker-containerd --config /var/run/docker/containerd/containerd.toml
> root      22868  11287  0 15:12 pts/0    00:00:00 grep --color=auto docker

### 运行docker

`docker`通过运行`hello-world` 映像验证是否已正确安装。



# 安装tomcat

## 搜索tomcat

> docker search tomcat

> NAME                                  DESCRIPTION                                     STARS         
> tomcat                                Apache Tomcat is an open source implementati…   2081          
> tomee                                 Apache TomEE is an all-Apache Java EE certif…   58            
> dordoka/tomcat                        Ubuntu 14.04, Oracle JDK 8 and Tomcat 8 base…   49            
> davidcaste/alpine-tomcat              Apache Tomcat 7/8 using Oracle Java 7/8 with…   31            
> bitnami/tomcat                        Bitnami Tomcat Docker Image                     25            
> consol/tomcat-7.0                     Tomcat 7.0.57, 8080, "admin/admin"              16            
> cloudesire/tomcat                     Tomcat server, 6/7/8                            15            
> tutum/tomcat                          Base docker image to run a Tomcat applicatio…   11            
> meirwa/spring-boot-tomcat-mysql-app   a sample spring-boot app using tomcat and My…   10            
> aallam/tomcat-mysql                   Debian, Oracle JDK, Tomcat & MySQL              8             
> jeanblanchard/tomcat                  Minimal Docker image with Apache Tomcat         8             
> rightctrl/tomcat                      CentOS , Oracle Java, tomcat application ssl…   3             
> maluuba/tomcat7-java8                 Tomcat7 with java8.                             3             
> arm64v8/tomcat                        Apache Tomcat is an open source implementati…   2             
> amd64/tomcat                          Apache Tomcat is an open source implementati…   2             
> jelastic/tomcat                       An image of the Tomcat Java application serv…   1             
> primetoninc/tomcat                    Apache tomcat 8.5, 8.0, 7.0                     1             
> camptocamp/tomcat-logback             Docker image for tomcat with logback integra…   1             
> 99taxis/tomcat7                       Tomcat7                                         1             
> fabric8/tomcat-8                      Fabric8 Tomcat 8 Image                          1             
> oobsri/tomcat8                        Testing CI Jobs with different names.           0             
> s390x/tomcat                          Apache Tomcat is an open source implementati…   0             
> cfje/tomcat-resource                  Tomcat Concourse Resource                       0             
> swisstopo/service-print-tomcat        backend tomcat for service-print "the true, …   0             
> picoded/tomcat7                       tomcat7 with jre8 and MANAGER_USER / MANAGER…   0   

## 安装tomcat

> docker pull  docker.io/tomcat 

## 查看安装的image

> docker images

> centos              latest              75835a67d134        6 days ago          200MB
> hello-world         latest              4ab4c602aa5e        5 weeks ago         1.84kB
> dordoka/tomcat      latest              61c7a8978788        6 weeks ago         796MB

## 启动tomcat

> docker run -p 8080:8080 docker.io/tomcat #  若端口被占用，可以指定容器和主机的映射端口 前者是外围访问端口：后者是容器内部端口



