---
title: mac-docker
date: 2018-10-26 18:08:37
tags:
- mac
- docker
---

### 1.命令
``` bash
#查看运行的容器
docker ps

#查看所有容器
docker ps -a

#进入容器
docker exec -it $container_id /bin/bash

#停用全部运行中的容器
docker stop $(docker ps -q)

#删除容器
docker rm $container_id

#删除全部容器
docker rm $(docker ps -aq)
```
<!-- more -->

### 2.删除全部容器、镜像

#### 2.1 停用删除容器
``` bash
docker stop $(docker ps -q) & docker rm $(docker ps -aq)
```

#### 2.2 删除异常停止的docker容器
``` bash
docker rm `docker ps -a | grep Exited | awk '{print $1}'` 
```

#### 2.3 删除名称或标签为none的镜像
``` bash
docker rmi -f  `docker images | grep '<none>' | awk '{print $3}'`
```
