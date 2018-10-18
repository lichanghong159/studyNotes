nRAP2是采用前后端分离的形式，因此搭建完整的RAP2需要 **服务端：**[rap2-delos](https://github.com/thx/rap2-delos)，**客户端：**[rap2-dolores](https://github.com/thx/rap2-dolores) 同时部署

# 安装基本工具

- [Git](https://git-scm.com/downloads)
- [Node 8.9.4+](https://nodejs.org/zh-cn/download)
- [Redis 4.0+](https://redis.io/download)
- [MySQL 5.7+](https://www.mysql.com/cn/downloads)

以上基本工具请根据自身需要，下载对应系统安装包，请自行解决安装配置等问题，这里不做过多说明

> Redis 安装可参考[Linux 常用应用安装](https://incoder.org/2018/05/15/linux-build)；
> Redis 最好用非安全模式启动

# 安装nodejs

## 上传nodejs安装包
## 执行解压命令
`tar -zxvf node-v8.11.4-linux-x64.tar.gz `
## 配置node环境变量
###  `vim /etc/profile`
```
在文件末尾增加
export NODE_HOME=/neworiental/rap2/node-v8.11.4-linux-x64
export PATH=$NODE_HOME/bin:$PATH
```
## 配置淘宝镜像

`npm install -g cnpm --registry=https://registry.npm.taobao.org`
### 执行`source /etc/profile`

让配置生效
# 安装git
## 执行命令:`yum install git -y`
# 创建数据库
登录mysql服务器创建数据库<br>
`CREATE DATABASE IF NOT EXISTS rap2_delos_app DEFAULT CHARSET utf8 COLLATE utf8_general_ci;`

# 服务端delos环境搭建
##  构建项目
构建项目前，请确认Node，Redis，MySQL服务均能正常使用<br>
`git clone https://github.com/thx/rap2-delos.git`
### 进入rap2-delos目录 cd rap2-delos/
安装项目依赖包
项目根目录下执行

### 修改配置文件
#### `vim src/config/config.dev.ts`

```
import { IConfigOptions } from "../types";

let config: IConfigOptions = {
  version: '2.3',
  serve: {
    port: 8080,
  },
  keys: ['some secret hurr'],
  session: {
    key: 'rap2:sess',
  },
  db: {
    dialect: 'mysql',
    host: '10.202.202.116',//修改为真实环境
    port: 3358,//修改为真实环境
    username: 'rap2',//修改为真实环境
    password: 'rap2',//修改为真实环境
    database: 'rap2_delos_app',//修改为真实环境
    pool: {
      max: 80,
      min: 0,
      idle: 20000,
    },
    logging: false,
  },
  redis: {//可以配置集群,具体的配置可以参考原始的config.prod.ts 文件
    host: '10.202.80.118',//修改为真实环境
    port: 7800,//修改为真实环境
    password: 'zhangzhen16@xg'//修改为真实环境
    }
  }


export default config

```
<br>
同样修改config.local.ts、config.prod.ts

* config.dev.ts 是开放环境的配置文件
* config.prod.ts 生产环境的配置文件
* config.local.ts 本地部署的配置文件

==根据不同的环境配置不同的配置文件==

==一定要注意空格问题，换行遇到缩进，使用两个空格==

### 安装项目所需依赖

`npm install --unsafe-perm --verbose`

==`--unsafe-perm`执行的时候不进行安全检查==

### 全局安装PM2

`npm install -g pm2`
### 安装TypeScript编译包

`npm install typescript -g`
如果下载缓慢，请使用淘宝npm镜像

`npm install -g cnpm --registry=https://registry.npm.taobao.org`


### 初始化数据库
执行mocha测试用例和js代码规范检查
`npm run check`

项目根目录下执行(该过程比较慢，耐心等待初始化完成)

`npm run create-db`

### 编译启动项目

#### 开发模式
启动开发模式的服务器 监视并在发生代码变更时自动重启(第一次运行比较慢，请耐心等待)

`npm run dev`
#### 生产模式

启动生产模式服务器

`npm start`

看到浏览器中如下提示，表示服务端delos已经部署成功

RAP2后端服务已启动，请从前端服务(rap2-dolores)访问。 RAP2 back-end server is started, please visit via front-end service (rap2-dolores).

或者在程序控制台出现如下Log，表示服务端delos已经部署成功

#### 查看运行日志

在服务器终端下使用`pm2 logs`即可查看运行日志

# 开放防火墙端口
`firewall-cmd --zone=public --add-port=8080/tcp --permanent`

`firewall-cmd --zone=public --add-port=80/tcp --permanent`

`systemctl restart firewalld.service`

# 客户端dolores环境搭建
## 获取源代码

`git clone https://github.com/thx/rap2-dolores.git`

## [安装xsel](https://centos.pkgs.org/7/epel-x86_64/xsel-1.2.0-15.el7.x86_64.rpm.html)

`rpm -ivh xsel-1.2.0-15.el7.x86_64.rpm `

## 环境配置
### 配置文件

目录：rap2-dolores/src/config

文件：config.dev.ts;其中dev，表示开发环境，其他同理

修改：config.dev.ts文件，serve地址是 服务端 rap2-delos 部署成功后的地址，默认：'http://localhost:8080'

==如果部署在同一台机器上一定不要使用127.0.0.1或者localhost。使用真是IP，这个调整了好久...==
## 启动项目

### 安装项目依赖包
 项目根目录下执行

`npm install --unsafe-perm --verbose ` 

==`--unsafe-perm`执行的时候不进行安全检查==

## 编译启动项目
### 开发模式
自动监视改变后重新编译

`npm run dev`

备注：测试用例

`npm run test`

### 生产模式
#### 编译React生产包

`npm run build`

用serve命令或nginx服务器路由到编译产出的build文件夹作为静态服务器即可

`npm install -g serve`

`serve -s ./build -p 80 & `

看到浏览器中出现登录页面，表示部署成功

# 用户登录

## 新注册用户

直接使用邮箱注册即可。

## 使用内置用户

数据库中内置用户的密码都是明文的，但是登录时传递的密码时加密的。会导致用户名/密码错误的提示。

### 解决方案

* 先注册一个新的用户
* 将刚注册用户的密码拷贝到想使用的账号上即可

