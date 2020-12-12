多阶段构建

网络：
Bridge（子网络里，宿主机器不可访问），host（宿主机器）

### 常用命令
```
run 指定命令会覆盖dockerfile里的CMD命令
run --entrypoint 会覆盖dockerfile里的ENTRYPOINT命令
```


```
$docker --version #查看版本
$docker-compose --version #查看版本
$docker-machine --version #查看版本
$docker version #查看client和server端版本，并可以查看是否开启体验功能



$docker ps # 查看当前正在运行的image实例 == docker container -ps == docker container -ls
$docker ps -a #查看所有镜像实例

$docker inspect <task or container>   检查任务或容器
```

#### 复制
```
docker cp 复制
docker cp storeConfig20190620.sql midas-store-admin_db_1:/tmp/
```
实例：
将主机/www/runoob目录拷贝到容器96f7f14e99ab的/www目录下
`docker cp /www/runoob 96f7f14e99ab:/www/`

将主机/www/runoob目录拷贝到容器96f7f14e99ab中，目录重命名为www
`docker cp /www/runoob 96f7f14e99ab:/www`

将容器96f7f14e99ab的/www目录拷贝到主机的/tmp目录中
`docker cp 96f7f14e99ab:/www /tmp/`
￼
#### volume
https://deepzz.com/post/the-docker-volumes-basic.html



### container
- 运行镜像，生成一个正在运行的容器实例
```
docker container run hello-world
```

实例：
```
docker container run -p 8000:3000 -it koa-demo /bin/bash
#或者
docker container run -p 8000:3000 -it koa-demo:0.0.1 bash

参数含义：
-p 容器3000端口映射到本机的8000端口
-it 容器的shell映射到当前shell，在本机窗口输入的命令会传入容器
koa-demo:0.0.1 image文件名 :后为标签，默认标签为latest
/bin/bash 容器启动后，内部执行的第一个命令。这个命令是启动bash，保证用户可以使用shell


source ./.env

docker run -d \
  -v $PWD/data/log:/usr/src/app/log \
  --net=host \
  --restart=always \
  --name=subscribe-assistant \
  --env-file .env \
  subscribe-assistant:$VERSION


pwd 进程运行时所在目录
$PWD 系统变量
```

| 参数 | 含义 |
| --- | ----- |
| -i | 以交互模式运行容器，通常与 -t 同时使用 | 
| -t | 为容器重新分配一个伪输入终端，通常与 -i 同时使用 |
| -d | 后台运行容器，并返回容器ID |
| -p | 指定端口映射，格式为：主机(宿主)端口:容器端口 |
| -P |	随机端口映射，容器内部端口随机映射到主机的高端口 |
| -v | |
| --name= | 为容器指定一个名称 |
| --dns 8.8.8.8 | 指定容器使用的DNS服务器，默认和宿主一致 |
| --dns-search | example.com	指定容器DNS搜索域名，默认和宿主一致 |
| -h mars | 指定容器的hostname |
| -e username=ritchie | 设置环境变量 |
| --env-file=[] | 从指定文件读入环境变量 |
| --cpuset="0-2" or --cpuset="0,1,2" | 绑定容器到指定CPU运行 |
| -m | 设置容器使用内存最大值 |
| --net=bridge | 指定容器的网络连接类型，支持 bridge/host/none/container: 四种类型 |
| --link=[] | 添加链接到另一个容器 |
| --expose=[] | 开放一个端口或一组端口 |
| --volume , -v | 绑定一个卷 |
| --restart | 设置容器的重启策略 | 

```
重启策略有：
    no，默认策略，在容器退出时不重启容器
    on-failure，在容器非正常退出时（退出状态非0），才会重启容器
    on-failure:3，在容器非正常退出时重启容器，最多重启3次
    always，在容器退出时总是重启容器
    unless-stopped，在容器退出时总是重启容器，但是不考虑在Docker守护进程启动时就已经停止了的容器
```



- 启动已经生成、已经停止运行的容器文件
```
docker container start [containerID]
```

- 停止所有的container（容器），这样才能够删除其中的images：
```
docker stop $(docker ps -a -q) 或者 docker stop $(docker ps -aq)
docker container kill [containID]
```

- 关闭/终止容器并不会删除容器文件，只是容器停止运行而已, 依然会占据硬盘空间

- 如果想要删除所有container（容器）的话再加一个指令：
```
docker container rm
docker rm $(docker ps -a -q) 或者 docker rm $(docker ps -aq)
```

#### 进入容器
`docker exec`
```
进入docker的mysql
docker exec -it midas-store-admin_db_1【容器name】  bash  
docker container exec -it [containerID] /bin/bash

mysql -uroot -p

use db_name;
source /tmp/XXX.sql
执行sql文件 

退出
exit
```

#### 导入导出
导出`容器`快照到本地文件
```
docker export 7691a814370e > ubuntu.tar
```

可以使用`docker import`从`容器`快照文件中再导入为`镜像`，例如
```
$ cat ubuntu.tar | docker import - test/ubuntu:v1.0
$ docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED              VIRTUAL SIZE
test/ubuntu         v1.0                9d37a6082e97        About a minute ago   171.3 MB
```
此外，也可以通过指定 URL 或者某个目录来导入，例如
```
docker import http://example.com/exampleimage.tgz example/imagerepo
```

#### save和load
保存`镜像`到本地文件
```
docker save -o images.tar postgres:9.6 mongo:3.4
```
打包之后的`images.tar`包含`postgres:9.6`和`mongo:3.4`这两个镜像。

可以使用`docker load`载入`镜像`
```
docker load -i images.tar

```

#### 更新容器镜像
```
1. 拉取最新镜像
docker pull XXX
或者将镜像文件import导出

2. 查看容器配置信息
docker inspect 8992319a1585

3. 停止容器
docker kill 8992319a1585

4. 删除容器
docker rm XXX

5. 生成容器
docker run --配置参数
```

### 多容器
#### 启动并运行整个应用程序（所有服务）
```
docker-compose up  (docker-compose.yml)
```
#### 关闭所有服务
```
docker-compose stop
```

#### compose模板文件
参考: https://yeasy.gitbooks.io/docker_practice/compose/compose_file.html
实例
```
version: "3"

#服务
#每个服务都必须通过 image 指令指定镜像或 build 指令（需要 Dockerfile）等来自动构建生成镜像。
services:

   db:
     image: mysql:5.6

     #mysql镜像dockerfile里最后ENTRYPOINT docker-entrypoint.sh脚本
     #docker-entrypoint.sh里有一段内容就是从docker-entrypoint-initdb.d目录下遍历所有的.sh和.sql后缀的文件，然后执行
     #所以将需要初始化的sql语句放到/data/init_db目录下
     volumes:
       - ./data/mysql:/var/lib/mysql
       - ./data/init_db:/docker-entrypoint-initdb.d
     ports:
       - "${DB_PORT}:3306"
     restart: always
     environment:
       MYSQL_ROOT_PASSWORD: root
       MYSQL_DATABASE: ${DB_NAME}
       MYSQL_USER: ${DB_USER}
       MYSQL_PASSWORD: ${DB_PASSWORD}

   web:
     #指定 Dockerfile 所在文件夹的路径
     build: .
     #解决容器的依赖、启动先后的问题。 先启动db再启动web
     depends_on:
       - db
     #数据卷所挂载路径设置。 
     #由（:）分隔的三个字段组成，<卷名>:<容器路径>:<选项列表>。选项列表，如：ro只读。
     #docker inspect containerid可查看volume挂载情况 Mounts:[]
     volumes:
       - ./data/logs:/usr/src/app/log
       - ./data/dest:/usr/src/app/dest
       - ./data/uploads:/usr/src/app/uploads
     ports:
       - "${PORT}:${PORT}"
     restart: always
     env_file:
       - .env

     #指定容器名称。默认将会使用 项目名称_服务名称_序号 这样的格式
     container_name: docker-web-container

```

### images

查看当前有些什么images（镜像）
`docker image list`
`docker images`

从仓库拉取image文件
`docker image pull library/hello-world`

删除images（镜像），通过image的id来指定删除谁
`docker image rm [imageName]`
`docker rmi <image id>`

想要删除untagged images，也就是那些id为的image的话可以用
```
docker rmi $(docker images | grep "^<none>" | awk "{print $3}")
```

要删除全部image（镜像）的话
`docker rmi $(docker images -q)`

强制删除全部image的话
`docker rmi -f $(docker images -q)`


chmod 755 game_goods.php
drwxr-xr-x   


#### 创建镜像
```
1 .dockerignore
2 Dockerfile
3 docker image build (-t 镜像名称； : 指定标签)

$ docker image build -t koa-demo .
或者
$ docker image build -t koa-demo:0.0.1 .
--network=host

最后的那个点表示 Dockerfile 文件所在的路径，上例是当前路径，所以是一个点
```
￼
#### Dockerfile示例
```
FROM node:8.9.4
# 使用node8.9.4镜像

# 设置时区
RUN echo "Asia/Shanghai" > /etc/timezone \
  && dpkg-reconfigure -f noninteractive tzdata
￼

# 修改sources.list源
RUN printf "deb http://archive.debian.org/debian/ jessie main\ndeb-src http://archive.debian.org/debian/ jessie main\ndeb http://security.debian.org jessie/updates main\ndeb-src http://security.debian.org jessie/updates main" > /etc/apt/sources.list

_________
#apt-get 换源
RUN sed -i 's/archive.ubuntu.com/mirrors.ustc.edu.cn/g' /etc/apt/sources.list
RUN apt-get update

#安装linux基本软件
RUN apt-get install -y vim bash-completion
RUN apt-get install -y nginx
RUN apt-get install -y git
#安装前端开发环境
RUN apt-get install -y curl
RUN curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
RUN sudo apt-get install -y nodejs
_________

WORKDIR /usr/src/app
COPY package*.json ./

# 切换到公司内部的npm仓库
RUN npm install --registry=http://r.tnpm.oa.com --proxy=http://r.tnpm.oa.com:80
COPY . .
RUN npm run build

CMD ["node", "server/app.js"]
```



#### 打包两种方式
1. 打包
docker image build -t store-ads-admin_web:v20200706 .
docker save -o store-ads-admin_web:v20200706.tar  store-ads-admin_web:v20200706

1. 上传docker.oa.com
docker image build -t store-ads-admin_web:v20200706 .
docker push docker.oa.com/midas-web/store-ads-admin_web:V1.0R220

#### 对应部署方式
一、
1. 执行`docker pull docker.oa.com/midas-web/store-ads-admin_web:{{version}}`，导入镜像文件到docker
2. 执行`docker images`，可以看到你刚刚导入的镜像
3. 执行`docker ps`，查看正在运行的docker实例，如果有该系统的运行实例，先通过`docker stop {{CONTAINER ID}}`停掉实例，然后`docker rm {{CONTAINER ID}}`销毁实例
4. 修改web.sh里store-ads-admin_web:$VERSION 改为docker.oa.com/midas-web/store-ads-admin_web:$VERSION
5. 修改`./env`中修改版本号为 V1.0R220
6. 运行NodeJS容器：`./web.sh`

二、
1. 要求开发同学提供构建好的镜像文件，或从work平台下载CI构建出来的镜像文件，扩展名是.tar
2. 将镜像文件上传到images目录下，文件命名规范是：{{tag}}:{{version}}，例如store-ads-admin_web:v20190627.tar
3. 执行docker load -i images/store-ads-admin_web:{{version}}.tar，导入镜像文件到docker
4. 执行docker images，可以看到你刚刚导入的镜像，tag就是版本号
5. 执行docker ps，查看正在运行的docker实例，如果有该系统的运行实例，先通过docker stop store-ads-admin_web:{{version}}停掉实例，然后docker rm store-ads-admin_web:{{version}}销毁实例
6. 修改.env中的版本号
7. 运行NodeJS容器：./web.sh




