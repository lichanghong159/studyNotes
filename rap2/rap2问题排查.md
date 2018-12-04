# 无法kill 掉node进程

通过`pm2 stop all`命令即可kill掉所有的node进程

# delos应用错误

通过`pm2 logs`查询日志

## 数据库连接错误

请检查数据库是否能正常连接

```
数据库地址:10.202.202.116
用户名:rap2
密码:rap2
数据库:rap2_delos_app
```

## redis错误

rap2现在默认连接的是redis的主
目前配置的redis是10.202.80.117。如果redis主从发生变化要修改`/neworiental/rap2/rap2-delos/src/config`下的文件:

> config.dev.ts  
>
> config.local.ts 
>
>  config.prod.ts

这三个文件都要修改

```shell
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
    host: '10.202.202.116',
    port: 3358,
    username: 'rap2',
    password: 'rap2',
    database: 'rap2_delos_app',
    pool: {
      max: 5,
      min: 0,
      idle: 10000,
    },
    logging: false,
  },
 
  redis: {
    host: '10.202.80.117', # 要修改此处的ip地址，注意文件格式不要发生变化，否则会有意向不到的惊喜
    port: 7800,
    password: 'zhangzhen16@xg'
    }
  }


export default config
```

修改完配置文件之后进入`/neworiental/rap2/rap2-delos`执行`npm run build`重新编译，然后在执行`npm start`即可。

> 注意：已经要在/neworiental/rap2/rap2-delos执行

