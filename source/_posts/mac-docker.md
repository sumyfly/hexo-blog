---
title: mac-docker
date: 2018-10-26 18:08:37
tags:
- mac
- docker
---

### 1. 基本命令
``` bash
# 运行镜像
docker run -t -i ubuntu:14.04 /bin/bash 

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

# 进入正在运行的容器
docker exec -it $container_id /bin/bash

# 给iamge改名
docker tag IMAGEID(镜像id) REPOSITORY:TAG（仓库：标签）
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

#### 4.1 docker-compose.yaml
valumes: $PWD:/app
就是把本地目录mount到容器中，相当于虚拟机的共享目录。

In order to map the volume to the host filesystem, you need to specify which path on the host should be mapped to the volume. You can do this in the docker-compose file using the volumes parameter. (Note: you can create volumes using docker-compose without declaring them in the Dockerfile.)

#### 4.3 docker-compose up vs docker-compose start
- docker-compose start
Starts existing containers for a service.

- docker-compose up
Builds, (re)creates, starts, and attaches to containers for a service.
Linked services will be started, unless they are already running.
By default, docker-compose up will aggregate the output of each container and, when it exits, all containers will be stopped. Running docker-compose up -d, will start the containers in the background and leave them running.
By default, if there are existing containers for a service, docker-compose up will stop and recreate them (preserving mounted volumes with volumes-from), so that changes in docker-compose.yml are picked up. If you do not want containers stopped and recreated, use `docker-compose up --no-recreate`. This will still start any stopped containers, if needed.

Source: https://docs.docker.com/v1.5/compose/cli/

https://stackoverflow.com/questions/33715499/what-is-the-difference-between-docker-compose-up-and-docker-compose-start


不过我执行`docker-compose down`后，默认就把容器给删除了，下次还是只能用`docker-compose up`.在`docker-compose up`后，运行`docker-compose stop`可以停用容器，之后可以用`docker-compose start`再次启用容器。

> docker-compose up会读取docker-compose.yaml，所以它会更新配置，它也会关闭正在运行的容器，创建新容器。而docker-compose start是启用已有容器，不会更新容器。
https://www.cnblogs.com/ityouknow/p/8648467.html

### 5 docker for mac
docker for mac是用了mac Os 中的hyperkit功能，不是使用vitural box.但是在做volumes时，映射到是虚拟机的/var/lib/docker/Data/xx目录，不是mac os的目录。如果是service中的volumes是可以用本地目录的。

#### 5.1删除volume
我想要删除虚拟机中的/var/lib/docker/Data/xx中的某个目录。
``` bash
# 查看volume
docker volume ls

# 删除volume
docker volume rm $volume_name

# 清理无用的volume
docker volume prune # 我清空了大约4.1G
```
https://www.digitalocean.com/community/tutorials/how-to-remove-docker-images-containers-and-volumes
> 查看mount映射信息,`docker inspect $container_id`，就可以发现volume的路径。
TODO:如果在`docker-compose.yml`中不指定ports,默认是不会导到宿主机器的，但是容器之间通讯是可以的。我的mysql连接不到，`lsof -i tcp:3306`，就是因为没有导出端口。

##### 5.2 关闭docker daemon
由于mac的docker是一个GUI程序，而且没有关闭按钮。所以可以从命令行关闭app.
``` bash
# 关闭docker
osascript -e 'quit app "Docker"'
# 启动docker
open -a Docker
```
refs:
https://danielkorn.io/post/restart-docker-mac/
