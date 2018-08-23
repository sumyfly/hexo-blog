---
title: mongoDB使用
date: 2018-08-23 14:34:55
tags:
---

> 下面的所述的是针对mongoDB4版本。
### 1.配置文件
Mac下在的tar文件，默认自带ssl，但是4.0版本默认不支持Openss 1.0版本，为了使用Openssl1.0版本所以配置文件中需要去掉SSL高版本的支持(disabledProtocols)，

<!-- more-->
mongod.conf文件
``` yaml
systemLog:
   destination: file
   path: "/Users/<YourName>/logs/mongodb/mongod.log"
   logAppend: true
storage:
   dbPath: "/Users/<YourName>/local/mongodb-data"
   journal:
      enabled: true
processManagement:
   fork: true
net:
   bindIp: 127.0.0.1
   port: 27017
   ssl:
      mode: "allowSSL"
      PEMKeyFile: "/Users/<YourName>/keystore/orbyAI/orbyAIServer.pem"
      PEMKeyPassword: ""
      disabledProtocols: "TLS1_1,TLS1_2"
setParameter:
   enableLocalhostAuthBypass: false
security:
   authorization: "enabled"
```

> 注意: security.authorization一开始不要加上，等启动后去admin的db建立一个user后再加上这条配置。

> pem文件必须带有crt和privateKey。

``` bash
# 创建pem过程
# 1.生成一个private key(.key)和certificate(.crt)
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out certificate.crt
# 2.打包crt和key，这个就可以给mongoDB服务器使用了
cat certificate.crt privateKey.key > server.pem
```

### 2. 启动mogoDB
#### 2.1 第一次启动mongDB，不带登录
mongod --config $HOME/etc/mongod.conf
注意不能使用mongos,它不支持某些配置。
#### 2.2 登录之后加上用户
``` bash
# 登录
mongo
# 添加用户
use admin
db.createUser(
  {
    user: "myUserAdmin",
    pwd: "abc123",
    roles: [ { role: "userAdminAnyDatabase", db: "admin" } ]
  }
)
```
#### 2.3 关闭mongoDB
``` bash
mongo --eval "db.getSiblingDB('admin').shutdownServer()"
```
#### 2.4 配置文件中加上auth
在mongod.conf中加上security.authorization: "enabled"

#### 2.5 带用户名密码登录
``` bash
mongo -u "myUserAdmin" -p "abc123" --authenticationDatabase "admin"
```
现在就没那个警告了：Access control is not enabled for the database.