---
title: Docker篇-2021年春招准备
date: 2021-02-03 12:11:32
tags:
---

#### 1. 什么是Docker？

虚拟化的工具，Docker、虚拟机

#### 2. Docker与虚拟机的区别:

- 虚拟机是在硬件的基础上虚拟出多个操作系统。传统虚拟机技术是虚拟出一套硬件后，在其上运行一个完整操作系统
- Docker容器没有自己的内核，在操作系统之上虚拟了一份文件系统，更加轻便。

![title](/images/2021年春招准备-Docker篇/2.png)

#### 3. Docker容器有几种状态

运行、已暂停、重新启动、已退出

```shell
docker ps -a
```

![title](/images/2021年春招准备-Docker篇/3.png)

#### 4．Dockerfile中最常见的指令是什么

```dockerfile
FROM node:8-slim  # 指定基础镜像
RUN apt-get update && apt-get install -y nginx # 运行指定的命令
WORKDIR /var/www/html
COPY ./build/ ./
RUN ls
EXPOSE 80
CMD ["nginx","-g","daemon off;"] # 容器启动时要运行的命令
```

#### 5. docker常用命令：

- docker pull 拉取或者更新指定镜像
- docker push 将镜像推送至远程仓库
- docker rm 删除容器
- docker rmi 删除镜像
- docker images 列出所有镜像
- docker ps 列出所有容器
- docker logs

- 容器与主机之间的数据拷贝命令

```shell
docker cp /www 96f7f14e99ab:/www/ #主机到容器
docker cp 96f7f14e99ab:/www /tmp/ #容器到主机
```

- 启动nginx容器

```shell
docker run 
-d # 后台运行返回容器ID
-P # 随机端口
-p # 宿主机:容器
--name nginx2 
-v /home/nginx:/usr/share/nginx/html #宿主机:容器   -v后边加“：ro” 限制容器的写入权限；
--rm # 退出时自动清理
nginx
```

- docker commit

```shell
docker commit 
--author "Tao Wang <twang2218@gmail.com>" # --author 是指定修改的作者
--message "修改了默认网页" # --message 则是记录本次修改的内容
webserver # <容器ID或容器名>
nginx:v2  # [<仓库名>[:<标签>]
```

- docker history 71fa7369f437   / dockviz 看树形结构

```shell
IMAGE          CREATED       CREATED BY                                      SIZE      COMMENT
71fa7369f437   6 years ago   /bin/sh -c #(nop) CMD [/bin/sh]                 0B        
<missing>      6 years ago   /bin/sh -c #(nop) ENV ENV=/root/.bashrc         0B        
<missing>      6 years ago   /bin/sh -c #(nop) ENV HOME=/root                0B        
<missing>      6 years ago   /bin/sh -c #(nop) ADD file:9775c32290ddfc3ef…   679B      
<missing>      6 years ago   /bin/sh -c #(nop) ADD file:8af1e65661badcfdf…   4.23MB    
<missing>      6 years ago   /bin/sh -c #(nop) MAINTAINER Brian Clements …   0B        
<missing>      7 years ago                                                   0B        Imported from -
```



#### 6. 解释一下Dockerfile的ONBUILD指令

以当前镜像为parent 镜像时触发

#### 7、**ADD/COPY区别**：

> 相同将本机文件拷贝到容器

- COPY: 仅仅是把本地的文件拷贝到容器镜像中，COPY 命令是最合适不过; 使用 COPY 命令把前一阶段构建的产物拷贝到另一个镜像中

  ```shell
  FROM golang:1.7.3
  WORKDIR /go/src/github.com/sparkdevo/href-counter/
  RUN go get -d -v golang.org/x/net/html
  COPY app.go    .
  RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o app .
  
  FROM alpine:latest
  RUN apk --no-cache add ca-certificates
  WORKDIR /root/
  COPY --from=0 /go/src/github.com/sparkdevo/href-counter/app .
  CMD ["./app"]
  ```

- ADD: 
  - 解压压缩文件并把它们添加到镜像中
  - 从 url 拷贝文件到镜像中

#### 8、Docker镜像大小分析

```dockerfile
FROM ubuntu:14.04
ADD compressed.tar /
RUN rm /compressed.tar
ADD compressed.tar /
```

1. **`FROM ubuntu:14.04`**：镜像 `ubuntu:14.04` 的大小为 200 MB；
2. **`ADD compressed.tar /`**： `compressed.tar` 文件为 100 MB，因此当前镜像层的大小为 100 MB，镜像总大小为 300 MB；
3. **`RUN rm /compressed.tar`**：删除文件 `compressed.tar`，此时的删除并不会删除下一层的 `compressed.tar` 文件，只会在当前层产生一个 `compressed.tar` 的删除标记，确保通过该层将看不到 `compressed.tar`，因此当前镜像层的大小也为 0，**镜像总大小为 300 MB**；但是加了一层镜像会变大？
4. **`ADD compressed.tar /`**：`compressed.tar` 文件为 100 MB，因此当前镜像层的大小为 300 MB + 100 MB，镜像总大小为 400 MB；

我们发现**镜像的总大小为 400 MB**，但是如果运行该镜像的话，我们很快可以发现在**容器**根目录下执行 `du -sh` 之后，显示的数值并非 400 MB，而**是 300 MB 左右**。主要的原因还是，**联合文件系统的性质保证了两个拥有 `compressed.tar` 文件的镜像层，容器仅能看到一个**



假设本地镜像存储中只有一个 `ubuntu:14.04` 的镜像，我们以两个 Dockerfile 来说明镜像复用：

```dockerfile
FROM ubuntu:14.04
RUN apt-get update
```
```dockerfile
FROM ubuntu:14.04
ADD compressed.tar /
```

假设最终 `docker build` 构建出来的镜像名分别为 `image 1` 和 `image 2`，由于两个 `Dockerfile` 均基于 `ubuntu:14.04`，因此，`image 1` 和 `image 2` 这两个镜像均复用了镜像 `ubuntu:14.04`。 假设 `RUN apt-get update` 修改的文件系统内容为 20 MB，最终本地三个镜像的大小关系应该如下：

**`ubuntu:14.04`**： 200 MB

**`image 1`**：200 MB（`ubuntu:14.04` 的大小）+ 20 MB = 220 MB

**`image 2`**：200 MB（`ubuntu:14.04` 的大小）+ 100 MB = 300 MB

如果仅仅是单纯的累加三个镜像的大小，那结果应该是：200 + 220 + 300 = 720 MB，但是由于镜像复用的存在，实际占用的磁盘空间大小是：**200 + 20 + 100 = 320 MB**，足足节省了 400 MB 的磁盘空间。在此，足以证明**镜像复用**的巨大好处。



#### 9. Docker Swarm

将一群Docker宿主机变成一个单一的虚拟主机，通过raft协议选举manager。

![img](/images/2021年春招准备-Docker篇/9.png)

#### 10. 监控Docker

- Docker统计数据：当我们使用容器ID调用docker stats时，我们获得容器的CPU，内存使用情况等。它类似于Linux中的top命令
- Docker events：Docker events是一个命令，用于查看Docker守护程序中正在进行的任务。

#### 11. Docker镜像和层有什么区别

- 镜像：Docker镜像是由一些列只读层构建的；

- 层：每个层代表镜像Dockerfile中的一条指令；

Docker的镜像由一层一层的文件系统组成，这种层级的文件系统就是UnionFS(OverlayFS)统一文件系统（union file system，升级版为AUFS). UnionFS是一种分层、轻量级并且高性能的文件系统。能够将不同的层整合成一个文件系统，为这些层提供了一个统一的视角，

联合加载会把各层文件系统叠加起来，这样最终的文件系统会包含所有底层的文件和目录。

> docker info

**运行态容器**为由一个可读写的文件系统「静态容器」+ 隔离的进程空间和其中的进程构成。

![img](/images/2021年春招准备-Docker篇/11.jpg)

#### 12. Docker默认的网络类型

- **host模式：**直接使用docker宿主机的网络。如IP地址、网卡类型等；

- **none模式：**不会给容器进行任何网络配置。也就是说，使用这种模式的容器没有IP地址（只有一个回环地址）；

- **bridge模式：**docker默认的网络配置，此模式会为每一个容器分配名称空间，可以设置IP，但是要与docker host主机的虚拟网络在同一网段；通过虚拟网卡与外界通信；

- **container模式：**这种模式与已经存在的容器共有同一个IP地址；

- **Network：**自定义网络。可以自行创建并且可以注定多种网络驱动，如bridge、overlay等；

#### 13. 如何控制容器占用系统资源（CPU，内存）的份额？
在使用docker create命令创建容器或使用docker run 创建并运行容器的时候，可以使用-c|–cpu-shares[=0]参数来调整同期使用CPU的权重，使用-m|–memory参数来调整容器使用内存的大小。

#### 14. 仓库（Repository）、注册服务器（Registry）、注册索引（Index）有何关系？
首先，仓库是存放一组关联镜像的集合，比如同一个应用的不同版本的镜像，注册服务器是存放实际的镜像的地方，注册索引则负责维护用户的账号，权限，搜索，标签等管理。注册服务器利用注册索引来实现认证等管理。

#### 15. Docker如何做资源隔离

- Namespace 技术，Linux中的PID是唯一且独立的，在正常情况下，用户不会看见重复的PID。然而在Docker采用了Namespace，从而令相同的PID可于不同的Namespace中独立存在
  - 缺点：隔离不彻底
- Cgroups可以限制、记录任务组所使用的物理资源（包括CPU，Memory，IO等）
- Rootfs做文件系统

#### 16. Docker、Runc、Shim、Containerd、Daemon之间的关系

![img](/images/2021年春招准备-Docker篇/12.gif)

- runc ：生来只有一个作用——创建容器，这一点它非常拿手，速度很快！runc 是 开放容器计划（OCI）容器运行时标准的参考实现
- shim：允许runC在启动容器之后退出
- containerd：所有的容器执行逻辑，它的主要任务是容器的生命周期管理——start | stop | pause | rm
- daemon 的主要功能包括镜像管理、镜像构建、REST API、身份验证、安全、核心网络以及编排



#### 17. Dockerfile减少构建镜像大小的方法

> 通过Dockerfile构建镜像时，很容易把镜像构建得很大。从通俗得原来上来说，一次RUN形成新的一层，如果没有在同一层删除，无论文件是否最后删除，都会带到下一层。

- 尽量在同一层运行更多的命令，比如:

```shell
RUN cp /usr/local/aa.tar.gz /opt
RUN tar xvf /opt/aa.tar.gz
RUN rm -rf /opt/aa.tar.gz
```

```shell
RUN cp /usr/local/aa.tar.gz /opt && \
       tar xvf /opt/aa.tar.gz && \
       rm -rf /opt/aa.tar.gz
```

- 如果在镜像中通过yum安装软件包，尽量在一行装完，不要多行，同样安装完后运行, yum clean all

- 不下载非必要依赖
- 将非运行环境删除，删除非运行环境时，需要与构建该环境在同一个RUN中，否则非运行环境仍然会被带入镜像中（以其中某一层的方式存在）
- 在 Docker 17.05 以上版本中，你可以使用 多阶段构建来减少所构建镜像的大小



#### 18. docker save和docker export区别

- docker save保存的是镜像（image，如果指定container背后的还是image），docker export保存的是容器（container）
- docker load用来载入镜像包，docker import用来载入容器包，但两者都会恢复为镜像
- docker load不能对载入的镜像重命名，而docker import可以为镜像指定新名称

