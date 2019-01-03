# 传统交付

传统交付支付软件，不交付环境。

# docker

Docker 是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的容器中，然后发布到任何流行的[Linux](https://baike.baidu.com/item/Linux)机器上，也可以实现虚拟化，容器是完全使用沙箱机制，相互之间不会有任何接口。

是一种容器技术，基于GO语言编写。

一个完整的Docker有以下几个部分组成：

1.  dockerClient客户端
2.  Docker Daemon守护进程
3.  Docker Image镜像
4.  DockerContainer容器 

## Docker能干什么

-   Web 应用的自动化打包和发布。
-   自动化测试和持续集成、发布。
-   在服务型环境中部署和调整数据库或其他的后台应用。
-   从头编译或者扩展现有的OpenShift或Cloud Foundry平台来搭建自己的PaaS环境。

## Docker与传统的虚拟化技术差异(vmware)

虚拟化技术就是虚拟了整套环境

缺点：资源占用多，启动慢



![1545568753416](docker-01.assets/1545568753416.png)



## Docker架构

Docker 使用客户端-服务器 (C/S) 架构模式，使用远程API来管理和创建Docker容器。

Docker 容器通过 Docker 镜像来创建。

容器与镜像的关系类似于面向对象编程中的对象与类。

| Docker | 面向对象 |
| ------ | -------- |
| 容器   | 对象     |
| 镜像   | 类       |

![img](docker-01.assets/576507-docker1.png)

| Docker 镜像(Images)    | Docker 镜像是用于创建 Docker 容器的模板。                    |
| ---------------------- | ------------------------------------------------------------ |
| Docker 容器(Container) | 容器是独立运行的一个或一组应用。                             |
| Docker 客户端(Client)  | Docker 客户端通过命令行或者其他工具使用 Docker[ API](<https://docs.docker.com/reference/api/docker_remote_api>) 与 Docker 的守护进程通信。 |
| Docker 主机(Host)      | 一个物理或者虚拟的机器用于执行 Docker 守护进程和容器。       |
| Docker 仓库(Registry)  | Docker 仓库用来保存镜像，可以理解为代码控制中的代码仓库。[Docker Hub](https://hub.docker.com/) 提供了庞大的镜像集合供使用。 |
| Docker Machine         | Docker Machine是一个简化Docker安装的命令行工具，通过一个简单的命令行即可在相应的平台上安装Docker，比如VirtualBox、 Digital Ocean、Microsoft Azure。 |

## docker网站

### [官网](http://www.docker.com)

### [中文网站](http://www.docker-cn.com)



### [仓库](https://hub.docker.com)

# docker命令

![](docker-01.assets/思维导图.jpg)

## 基础命令

### 命令格式

>   docker [OPTIONS] COMMAND

#### Options:

      * --config string     客户端配置文件的位置（默认为“/root/.docker”）
      * -D, --debug              启用调试模式
      * -H, --host list          要连接的守护程序套接字
      * -l, --log-level string   设置日志记录级别（“debug”|“info”|“warn”|“error”|“fatal”）（默认为“info”）
      * --tls                使用TLS; implied by --tlsverify
      *  --tlscacert string   仅由此CA签名的信任证书（默认为“/root/.docker/ca.pem”）
    
      * --tlscert string     TLS证书文件的路径（默认为“/root/.docker/cert.pem”）
      *  --tlskey string     TLS密钥文件的路径（默认为“/root/.docker/key.pem”）
    
      * --tlsverify         使用TLS并验证远程
      * -v, --version            打印版本信息并退出

## 帮助命令

*   docker version:查看版本
*   docker --help: 获取命令信息
*   docker info :基本信息

## 镜像命令

*   docker images :列出所有镜像

    *   -a :列出所有的镜像

    *   -q :只显示镜像ID

    *   --digests :显示摘要信息

    *   ## --no-trunc :完整的信息

*   docker search 镜像名 ： 从远程仓库中搜索镜像

*   docker pull 镜像名 ： 拉取镜像

*   docker rmi 镜像id ：删除镜像

    *   docker rmi -f 镜像名 ：强制删除
    *   docker rmi -f $(docker images -q)：删除所有镜像


## 容器命令

### 容器生命周期管理

#### **docker run**

创建一个新的容器并运行一个命令

```shell
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

OPTIONS说明：

-   **-a stdin:** 指定标准输入输出内容类型，可选 STDIN/STDOUT/STDERR 三项；
-   **-d:** 后台运行容器，并返回容器ID；
-   **-i:** 以交互模式运行容器，通常与 -t 同时使用；
-   **-p:** 端口映射，格式为：主机(宿主)端口:容器端口
-   **-t:** 为容器重新分配一个伪输入终端，通常与 -i 同时使用；
-   **--name="nginx-lb":** 为容器指定一个名称；
-   **--dns 8.8.8.8:** 指定容器使用的DNS服务器，默认和宿主一致；
-   **--dns-search example.com:** 指定容器DNS搜索域名，默认和宿主一致；
-   **-h "mars":** 指定容器的hostname；
-   **-e username="ritchie":** 设置环境变量；
-   **--env-file=[]:** 从指定文件读入环境变量；
-   **--cpuset="0-2" or --cpuset="0,1,2":** 绑定容器到指定CPU运行；
-   **-m :**设置容器使用内存最大值；
-   **--net="bridge":** 指定容器的网络连接类型，支持 bridge/host/none/container: 四种类型；
-   **--link=[]:** 添加链接到另一个容器；
-   **--expose=[]:** 开放一个端口或一组端口；

##### 实例

使用docker镜像nginx:latest以后台模式启动一个容器,并将容器命名为mynginx。

```
docker run --name mynginx -d nginx:latest
```

使用镜像nginx:latest以后台模式启动一个容器,并将容器的80端口映射到主机随机端口。

```
docker run -P -d nginx:latest
```

使用镜像 nginx:latest，以后台模式启动一个容器,将容器的 80 端口映射到主机的 80 端口,主机的目录 /data 映射到容器的 /data。

```
docker run -p 80:80 -v /data:/data -d nginx:latest
```

绑定容器的 8080 端口，并将其映射到本地主机 127.0.0.1 的 80 端口上。

```
$ docker run -p 127.0.0.1:80:8080/tcp ubuntu bash
```

使用镜像nginx:latest以交互模式启动一个容器,在容器内执行/bin/bash命令。

```
runoob@runoob:~$ docker run -it nginx:latest /bin/bash
root@b8573233d675:/# 
```

####  start/stop/restart 命令

**docker start** :启动一个或多个已经被停止的容器

**docker stop** :停止一个运行中的容器

**docker restart** :重启容器

##### 语法

```
docker start [OPTIONS] CONTAINER [CONTAINER...]
docker stop [OPTIONS] CONTAINER [CONTAINER...]
docker restart [OPTIONS] CONTAINER [CONTAINER...]
```

##### 实例

启动已被停止的容器myrunoob

```
docker start myrunoob
```

停止运行中的容器myrunoob

```
docker stop myrunoob
```

重启容器myrunoob

```
docker restart myrunoob
```

#### kill 命令

**docker kill** :杀掉一个运行中的容器。

##### 语法

```
docker kill [OPTIONS] CONTAINER [CONTAINER...]
```

OPTIONS说明：

-   **-s :**向容器发送一个信号

##### 实例

杀掉运行中的容器mynginx

```
runoob@runoob:~$ docker kill -s KILL mynginx
mynginx
```

#### rm 命令

**docker rm ：**删除一个或多少容器

##### 语法

```
docker rm [OPTIONS] CONTAINER [CONTAINER...]
```

OPTIONS说明：

-   **-f :**通过SIGKILL信号强制删除一个运行中的容器
-   **-l :**移除容器间的网络连接，而非容器本身
-   **-v :**-v 删除与容器关联的卷

##### 实例

强制删除容器db01、db02

```
docker rm -f db01 db02
```

移除容器nginx01对容器db01的连接，连接名db

```
docker rm -l db 
```

删除容器nginx01,并删除容器挂载的数据卷

```
docker rm -v nginx01
```

#### pause/unpause 命令

**docker pause** :暂停容器中所有的进程。

**docker unpause** :恢复容器中所有的进程。

##### 语法

```
docker pause [OPTIONS] CONTAINER [CONTAINER...]
docker unpause [OPTIONS] CONTAINER [CONTAINER...]
```

##### 实例

暂停数据库容器db01提供服务。

```
docker pause db01
```

恢复数据库容器db01提供服务。

```
docker unpause db01
```

#### create 命令

**docker create ：**创建一个新的容器但不启动它

用法同 [docker run](http://www.runoob.com/docker/docker-run-command.html)

##### 语法

```
docker create [OPTIONS] IMAGE [COMMAND] [ARG...]
```

语法同 [docker run](http://www.runoob.com/docker/docker-run-command.html)

##### 实例

使用docker镜像nginx:latest创建一个容器,并将容器命名为myrunoob

```
runoob@runoob:~$ docker create  --name myrunoob  nginx:latest      
09b93464c2f75b7b69f83d56a9cfc23ceb50a48a9db7652ee4c27e3e2cb1961f
```

#### exec 命令

**docker exec ：**在运行的容器中执行命令

##### 语法

```
docker exec [OPTIONS] CONTAINER COMMAND [ARG...]
```

OPTIONS说明：

-   **-d :**分离模式: 在后台运行
-   **-i :**即使没有附加也保持STDIN 打开
-   **-t :**分配一个伪终端

##### 实例

在容器mynginx中以交互模式执行容器内/root/runoob.sh脚本



```
runoob@runoob:~$ docker exec -it mynginx /bin/sh /root/runoob.sh
http://www.runoob.com/
```

在容器mynginx中开启一个交互模式的终端

```
runoob@runoob:~$ docker exec -i -t  mynginx /bin/bash
root@b1a0703e41e7:/#
```

### 容器操作

#### ps 命令

**docker ps :** 列出容器

##### 语法

```
docker ps [OPTIONS]
```

OPTIONS说明：

-   **-a :**显示所有的容器，包括未运行的。

-   **-f :**根据条件过滤显示的内容。

-   **--format :**指定返回值的模板文件。

-   **-l :**显示最近创建的容器。

-   **-n :**列出最近创建的n个容器。

-   **--no-trunc :**不截断输出。

-   **-q :**静默模式，只显示容器编号。

-   **-s :**显示总的文件大小。

##### 实例

列出所有在运行的容器信息。

```
runoob@runoob:~$ docker ps
CONTAINER ID   IMAGE          COMMAND                ...  PORTS                    NAMES
09b93464c2f7   nginx:latest   "nginx -g 'daemon off" ...  80/tcp, 443/tcp          myrunoob
96f7f14e99ab   mysql:5.6      "docker-entrypoint.sh" ...  0.0.0.0:3306->3306/tcp   mymysql
```

列出最近创建的5个容器信息。

```
runoob@runoob:~$ docker ps -n 5
CONTAINER ID        IMAGE               COMMAND                   CREATED           
09b93464c2f7        nginx:latest        "nginx -g 'daemon off"    2 days ago   ...     
b8573233d675        nginx:latest        "/bin/bash"               2 days ago   ...     
b1a0703e41e7        nginx:latest        "nginx -g 'daemon off"    2 days ago   ...    
f46fb1dec520        5c6e1090e771        "/bin/sh -c 'set -x \t"   2 days ago   ...   
a63b4a5597de        860c279d2fec        "bash"                    2 days ago   ...
```

列出所有创建的容器ID。

```
runoob@runoob:~$ docker ps -a -q
09b93464c2f7
b8573233d675
b1a0703e41e7
f46fb1dec520
a63b4a5597de
6a4aa42e947b
de7bb36e7968
43a432b73776
664a8ab1a585
ba52eb632bbd
...
```

#### inspect 命令

**docker inspect :** 获取容器/镜像的元数据。

##### 语法

```
docker inspect [OPTIONS] NAME|ID [NAME|ID...]
```

OPTIONS说明：

-   **-f :**指定返回值的模板文件。
-   **-s :**显示总的文件大小。
-   **--type :**为指定类型返回JSON。

##### 实例

获取镜像mysql:5.6的元信息。

```
runoob@runoob:~$ docker inspect mysql:5.6
[
    {
        "Id": "sha256:2c0964ec182ae9a045f866bbc2553087f6e42bfc16074a74fb820af235f070ec",
        "RepoTags": [
            "mysql:5.6"
        ],
        "RepoDigests": [],
        "Parent": "",
        "Comment": "",
        "Created": "2016-05-24T04:01:41.168371815Z",
        "Container": "e0924bc460ff97787f34610115e9363e6363b30b8efa406e28eb495ab199ca54",
        "ContainerConfig": {
            "Hostname": "b0cf605c7757",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "3306/tcp": {}
            },
...
```

获取正在运行的容器mymysql的 IP。

```
runoob@runoob:~$ docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' mymysql
172.17.0.3
```

#### top

**docker top :**查看容器中运行的进程信息，支持 ps 命令参数。

##### 语法

```
docker top [OPTIONS] CONTAINER [ps OPTIONS]
```

容器运行时不一定有/bin/bash终端来交互执行top命令，而且容器还不一定有top命令，可以使用docker top来实现查看container中正在运行的进程。

##### 实例

查看容器mymysql的进程信息。

```
runoob@runoob:~/mysql$ docker top mymysql
UID    PID    PPID    C      STIME   TTY  TIME       CMD
999    40347  40331   18     00:58   ?    00:00:02   mysqld
```

查看所有运行容器的进程信息。

```
for i in  `docker ps |grep Up|awk '{print $1}'`;do echo \ &&docker top $i; done
```

#### attach

**docker attach :**连接到正在运行中的容器。

##### 语法

```
docker attach [OPTIONS] CONTAINER
```

要attach上去的容器必须正在运行，可以同时连接上同一个container来共享屏幕（与screen命令的attach类似）。

官方文档中说attach后可以通过CTRL-C来detach，但实际上经过我的测试，如果container当前在运行bash，CTRL-C自然是当前行的输入，没有退出；如果container当前正在前台运行进程，如输出nginx的access.log日志，CTRL-C不仅会导致退出容器，而且还stop了。这不是我们想要的，detach的意思按理应该是脱离容器终端，但容器依然运行。好在attach是可以带上--sig-proxy=false来确保CTRL-D或CTRL-C不会关闭容器。

##### 实例

容器mynginx将访问日志指到标准输出，连接到容器查看访问信息。

```
runoob@runoob:~$ docker attach --sig-proxy=false mynginx
192.168.239.1 - - [10/Jul/2016:16:54:26 +0000] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/45.0.2454.93 Safari/537.36" "-"
```

#### events

**docker events :** 从服务器获取实时事件

##### 语法

```
docker events [OPTIONS]
```

OPTIONS说明：

-   **-f ：**根据条件过滤事件；
-   **--since ：**从指定的时间戳后显示所有事件;
-   **--until ：**流水时间显示到指定的时间为止；

##### 实例

显示docker 2016年7月1日后的所有事件。

```
runoob@runoob:~/mysql$ docker events  --since="1467302400"
2016-07-08T19:44:54.501277677+08:00 network connect 66f958fd13dc4314ad20034e576d5c5eba72e0849dcc38ad9e8436314a4149d4 (container=b8573233d675705df8c89796a2c2687cd8e36e03646457a15fb51022db440e64, name=bridge, type=bridge)
2016-07-08T19:44:54.723876221+08:00 container start b8573233d675705df8c89796a2c2687cd8e36e03646457a15fb51022db440e64 (image=nginx:latest, name=elegant_albattani)
2016-07-08T19:44:54.726110498+08:00 container resize b8573233d675705df8c89796a2c2687cd8e36e03646457a15fb51022db440e64 (height=39, image=nginx:latest, name=elegant_albattani, width=167)
2016-07-08T19:46:22.137250899+08:00 container die b8573233d675705df8c89796a2c2687cd8e36e03646457a15fb51022db440e64 (exitCode=0, image=nginx:latest, name=elegant_albattani)
...
```

显示docker 镜像为mysql:5.6 2016年7月1日后的相关事件。

```
runoob@runoob:~/mysql$ docker events -f "image"="mysql:5.6" --since="1467302400" 
2016-07-11T00:38:53.975174837+08:00 container start 96f7f14e99ab9d2f60943a50be23035eda1623782cc5f930411bbea407a2bb10 (image=mysql:5.6, name=mymysql)
2016-07-11T00:51:17.022572452+08:00 container kill 96f7f14e99ab9d2f60943a50be23035eda1623782cc5f930411bbea407a2bb10 (image=mysql:5.6, name=mymysql, signal=9)
2016-07-11T00:51:17.132532080+08:00 container die 96f7f14e99ab9d2f60943a50be23035eda1623782cc5f930411bbea407a2bb10 (exitCode=137, image=mysql:5.6, name=mymysql)
2016-07-11T00:51:17.514661357+08:00 container destroy 96f7f14e99ab9d2f60943a50be23035eda1623782cc5f930411bbea407a2bb10 (image=mysql:5.6, name=mymysql)
2016-07-11T00:57:18.551984549+08:00 container create c8f0a32f12f5ec061d286af0b1285601a3e33a90a08ff1706de619ac823c345c (image=mysql:5.6, name=mymysql)
2016-07-11T00:57:18.557405864+08:00 container attach c8f0a32f12f5ec061d286af0b1285601a3e33a90a08ff1706de619ac823c345c (image=mysql:5.6, name=mymysql)
2016-07-11T00:57:18.844134112+08:00 container start c8f0a32f12f5ec061d286af0b1285601a3e33a90a08ff1706de619ac823c345c (image=mysql:5.6, name=mymysql)
2016-07-11T00:57:19.140141428+08:00 container die c8f0a32f12f5ec061d286af0b1285601a3e33a90a08ff1706de619ac823c345c (exitCode=1, image=mysql:5.6, name=mymysql)
2016-07-11T00:58:05.941019136+08:00 container destroy c8f0a32f12f5ec061d286af0b1285601a3e33a90a08ff1706de619ac823c345c (image=mysql:5.6, name=mymysql)
2016-07-11T00:58:07.965128417+08:00 container create a404c6c174a21c52f199cfce476e041074ab020453c7df2a13a7869b48f2f37e (image=mysql:5.6, name=mymysql)
2016-07-11T00:58:08.188734598+08:00 container start a404c6c174a21c52f199cfce476e041074ab020453c7df2a13a7869b48f2f37e (image=mysql:5.6, name=mymysql)
2016-07-11T00:58:20.010876777+08:00 container top a404c6c174a21c52f199cfce476e041074ab020453c7df2a13a7869b48f2f37e (image=mysql:5.6, name=mymysql)
2016-07-11T01:06:01.395365098+08:00 container top a404c6c174a21c52f199cfce476e041074ab020453c7df2a13a7869b48f2f37e (image=mysql:5.6, name=mymysql)
```

 如果指定的时间是到秒级的，需要将时间转成时间戳。如果时间为日期的话，可以直接使用，如--since="2016-07-01"。

#### logs

**docker logs :** 获取容器的日志

### 语法

```
docker logs [OPTIONS] CONTAINER
```

OPTIONS说明：

-   **-f :** 跟踪日志输出
-   **--since :**显示某个开始时间的所有日志
-   **-t :** 显示时间戳
-   **--tail :**仅列出最新N条容器日志

### 实例

跟踪查看容器mynginx的日志输出。

```
runoob@runoob:~$ docker logs -f mynginx
192.168.239.1 - - [10/Jul/2016:16:53:33 +0000] "GET / HTTP/1.1" 200 612 "-" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/45.0.2454.93 Safari/537.36" "-"
2016/07/10 16:53:33 [error] 5#5: *1 open() "/usr/share/nginx/html/favicon.ico" failed (2: No such file or directory), client: 192.168.239.1, server: localhost, request: "GET /favicon.ico HTTP/1.1", host: "192.168.239.130", referrer: "http://192.168.239.130/"
192.168.239.1 - - [10/Jul/2016:16:53:33 +0000] "GET /favicon.ico HTTP/1.1" 404 571 "http://192.168.239.130/" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/45.0.2454.93 Safari/537.36" "-"
192.168.239.1 - - [10/Jul/2016:16:53:59 +0000] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/45.0.2454.93 Safari/537.36" "-"
...
```

查看容器mynginx从2016年7月1日后的最新10条日志。

```
docker logs --since="2016-07-01" --tail=10 mynginx
```

# wait 