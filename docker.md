# Docker



## 相关资料

https://docs.docker.com/reference/

https://www.runoob.com/docker/docker-tutorial.html

https://yeasy.gitbook.io/docker_practice/



## 基本概念

**镜像 Image**

1. 镜像可以从DockerHub下载，有很多封装好的各种环境，类似操作系统镜像。
2. 镜像有名字和Tag属性，Tag通常即版本。

**容器 Container**

1. 容器从镜像创建，在镜像文件基础上会有自己修改过的文件。
2. 容器有名字和id。
3. docker 中的容器是轻量级的，随时可以创建删除，一个容器通常运行一个微服务。

**容器的Entrypoint与1号进程**

1. 容器启动时会运行Entrypoint启动1号进程（即PID为1），Entrypoint可用默认值或自己指定。
2. 1号进程执行结束，则容器终止运行。
3. 退出1号进程但保持其运行：先按Ctrl+P，再按Ctrl+Q。
4. 退出1号进程并停止运行：exit，或Ctrl+D。
5. 进入1号进程：使用attach命令。
6. Linux容器如果Entrypoint没有指定为init system，则service/systemctl等命令无效。
7. 使用exec在启动的容器中运行程序，exit不会终止1号进程，也就不会终止容器。

https://www.cnblogs.com/sparkdev/p/7598590.html



## 设计理念

1. 一般不用Linux的init system，而直接把进程放到前台运行（即指定到Entrypoint）。
2. 需要在Linux中配置自己的东西作为Entrypoint，两种正常做法：在容器里配置好后commit新镜像，或者使用build创建镜像。最后从镜像运行新的容器。还有一个Hack办法是改运行中容器的json文件修改Entrypoint。



## 基础操作：创建运行容器

**注意：**

- 大部分docker操作都需要sudo权限，下文省略。
- `CONTAINER`指container的name或id，id输入前面几位能区分即可，`CONTAINER_NAME`特指name。
- `IMAGE`指image的name或id（通常用name）。

```bash
# 下载ubuntu镜像，创建、首次启动容器、进入容器shell
# docker run = docker container run
# docker container run [OPTIONS] IMAGE [COMMAND] [ARG...]
docker run -it --name CONTAINER_NAME IMAGE bash

# 退出容器shell但保持容器运行: 先按Ctrl+P，再按Ctrl+Q

# 退出容器shell并退出容器: exit 或 Ctrl+D
```



## 常用操作 pull/container/exec/attach

```bash
# 下载镜像，latest版本
docker pull ubuntu

# 创建、首次启动容器，使用daemon模式。
# 首次启动如果用start，任务结束会立即退出。
# docker run = docker container run
# docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
docker [container] run -itd --name CONTAINER_NAME IMAGE

# 创建容器
docker container create --name CONTAINER_NAME IMAGE

# 查看创建的容器，--all参数包含未运行容器
docker container ls --all
# or
docker container ls -a
# or
docker ps -a

# 查看容器详细信息
docker [container] inspect CONTAINER

# 启动/停止/重启/强制停止已有容器
docker container start/stop/restart/kill CONTAINER

# 进入运行中的容器，并运行bash，退出不会停止容器
docker exec -it CONTAINER bash

# 进入运行中的容器前台，直接退出会停止容器，使用 Ctrl+P 加 Ctrl+Q 则容器保持运行
docker attach CONTAINER
```



## 文件管理

```bash
# 容器和主机之间复制文件
docker cp CONTAINER:/file/path/within/container /host/path/target
```



## ps与批量操作

```bash
# 显示运行容器
docker ps
# 显示所有容器
docker ps -a
# 只显示id
docker ps -q
```



```bash
# 停止运行容器
docker container stop $(docker ps -q)

# 更新所有容器
docker update --restart=unless-stopped $(docker ps -q -a)
```



## run和update参数

run参数

```bash
# name
docker run --name CONTAINER_NAME IMAGE

# restart
# always: 始终启动容器
# unless-stopped: 除非手动停止，某则在出错终止/docker启动时启动容器
docker run --restart=unless-stopped IMAGE

# 端口绑定
docker run -p <host_port1>:<container_port1> -p <host_port2>:<container_port2> IMAGE
```

参数更新

```bash
docker rename CONTAINER_NAME NEW_CONTAINER_NAME
docker update --restart=unless-stopped CONTAINER
```



## 修改其他参数

一些参数只能在create和run中设置，不能update，例如端口绑定。之后如需修改有两个办法：

重新创建

```bash
# 提交镜像
docker commit xxx
# 重新运行容器
docker run xxx
```

改json文件

```bash
# 需要先停用docker
service docker stop
# 修改文件
vim /var/lib/docker/containers/<id>/config.v2.json
# 启动docker
service docker start
```



## Log

```bash
# 慎用：查看进程0的所有Log。如果Log很多可能要输出很久。
docker logs CONTAINER

# 参数
# --follow, -f 持续输出新Log
# --tail, -n 查看最后几行

# 查看最后10行并持续输出新Log
docker logs CONTAINER -f -n 10
```



## 使用commit创建镜像

1. 对于运行中的容器，可以使用commit在其基础上创建镜像，也有备份容器的作用。
2. 概念和git相似，镜像相当于commit，容器相当于在commit基础上加未提交的更改。

```bash
# 查看文件修改
docker diff CONTAINER
# 提交修改到镜像
docker commit -a "AUTHER" -m "MESSAGE" CONTAINER NEW_IMAGE
# 查看镜像的history
docker history IMAGE
```



## 使用Dockerfile编译镜像

Dockerfile格式

```bash
FROM ubuntu # 指定镜像

COPY hom*.txt /root/ # 复制文件到容器内，支持通配符

# Build时运行命令。每个RUN会新建一个commit，所以尽量合并RUN命令
RUN apt update \
&& apt install -y sudo vim curl \
&& apt clean

# ENTRYPOINT和CMD由Dockerfile指定默认值，执行run时可覆盖
# docker run --entrypoint ENTRYPOINT IMAGE CMD [ARG...]
# 实际运行的是 ENTRYPOINT CMD
ENTRYPOINT ["nginx", "-c"]
CMD ["/etc/nginx/nginx.conf"]

ENV NODE_VERSION 7.2.0 # 环境变量
```

创建一个目录，放入Dockerfile和编译需要的文件（例如COPY指定的文件），编译镜像

```bash
# 最后一个参数`.`为上下文路径，也就是创建的目录
docker build -t IMAGE_NAME .
```

CMD和ENTRYPOINT区别  https://ithelp.ithome.com.tw/articles/10250988

### 举例：构建一个灵活的Ubuntu

构建一个灵活的Ubuntu，自带很简单的init system，可以灵活配置自己想要的东西。

```bash
FROM ubuntu
RUN apt update && apt install -y sudo curl nano && apt clean
COPY start.sh /root
ENTRYPOINT /root/start.sh
```

start.sh (chmod+x)，里面只执行bash，容器启动后可以灵活添加自己的程序。

```bash
#!/usr/bin/env bash
cd /root/
echo "Hello"
# 在这里添加自己的程序
# ...
# 如果自己的程序意外停止，继续执行bash保持容器运行，方便修复环境等
bash
```

构建

```bash
docker build -t ubuntu-starter .
```

创建运行容器

```bash
docker run -it --name CONTAINER_NAME ubuntu-starter
```



## 常见问题

### Ubuntu镜像常用工具安装

```bash
apt update && apt upgrade -y && apt install -y wget curl vim sudo
```



### service / systemd / systemctl不能用

```bash
System has not been booted with systemd as init system (PID 1). Can't operate.
Failed to connect to bus: Host is down
```

按照docker的理念，不建议用init system，直接运行在前台。

https://stackoverflow.com/questions/39169403/systemd-and-systemctl-within-ubuntu-docker-images
https://askubuntu.com/questions/813588/systemctl-failed-to-connect-to-bus-docker-ubuntu16-04-container

一定要用，常见解决方法有（未验证）：

1. 使用 [phusion.baseimage](https://github.com/phusion/baseimage-docker) ，包含了自己的 init system，需要指定Entrypoint为init system的入口。
2. Ubuntu中可使用[docker-systemctl-replacement](https://github.com/gdraheim/docker-systemctl-replacement) 替代systemd，但是同样需要配置好后指定Entrypoint。
3. 使用centos镜像，指定Entrypoint为init system，同时设置privileged参数。

网上的做法没有用，因为没有指定Entrypoint： `docker run -it --name xxx --privileged=true ubuntu bash`。

