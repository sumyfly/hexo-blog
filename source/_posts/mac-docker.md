---
title: mac-docker
date: 2018-10-26 18:08:37
tags:
- mac
- docker
---

### 1. 基本命令
``` bash
# 查看运行的容器
docker ps

# 查看所有容器
docker ps -a

# 进入容器
docker exec -it $container_id /bin/bash

# 停用某个容器
docker stop $container_id

# 停用全部运行中的容器
docker stop $(docker ps -q)

# 杀死所有running状态的容器
docker kill $(docker ps -q)

# 查看镜像，不包含中间镜像
docker images

# 查看所有镜像,其中包含中间镜像
docker images -a
```
<!-- more -->

### 2. 删除容器

#### 2.1 删除容器
``` bash
docker rm $container_id
```

#### 2.2 删除全部容器
```bash
docker rm $(docker ps -aq)
```

#### 2.3 删除停止的容器
``` bash
docker rm $(docker ps --all -q -f status=exited)
# or
docker rm `docker ps -a | grep Exited | awk '{print $1}'` 
```

#### 2.4 停用并删除所有容器
``` bash
docker stop $(docker ps -q) & docker rm $(docker ps -aq)
```



### 3. 删除镜像

#### 3.1 删除镜像，先要删除对应容器
``` bash
docker ps -a # 查看所有container
docker rm $container_id # 删除容器 
docker rmi $image_id
# 或者先用docker rmi imageid进行删除，会报错被哪个容器引用，然后使用docker rm container_id删除掉该容器即可
```

#### 3.2 删除所有镜像
``` bash
docker rmi $(docker images -q)
```

#### 3.3 删除没有使用的镜像
``` bash
docker images -q --filter "dangling=true" | xargs -t --no-run-if-empty docker rmi
```

#### 3.4 删除名称或标签为none的镜像
``` bash
docker rmi -f  `docker images | grep '<none>' | awk '{print $3}'`
```

#### 3.5 删除`test_`开头的镜像：
``` bash
docker rmi -f $(docker images --format "{{.Repository}}" |grep "^test_*")
```

#### 3.6 删除所有\'untagged/dangling\' ()状态的镜像
``` bash
docker rmi $(docker images -q -f dangling=true)
```

### 4. docker-compose
docker-compose up启动的容器会自动在后面加上`_1`，这个是为了scale。因为docker-compose是编排工具，不是只管理单个镜像。











