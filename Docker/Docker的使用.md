# Docker 的使用

## 什么是 Docker

* Docker 是世界领先的软件容器平台，基于 __Go 语言__ 进行开发实现。
* Docker 能够自动执行重复性任务，例如搭建和配置开发环境，从而解放开发人员。
* 用户可以方便地创建和使用容器，把自己的应用放入容器。容器还可以进行版本管理、复制、分享、修改，就像管理普通的代码一样。
* Docker 可以__对进程进行封装隔离，属于操作系统层面的虚拟化技术。__ 由于隔离的进程独立于宿主和其它的隔离的进程，因此也称其为容器。

## 常见的概念

### 镜像

镜像就是一个只读的模板，镜像可以用来创建 Docker 容器，一个镜像可以创建多个容器

Docker 中的镜像是分层的，可复用的，而非简单的一堆文件迭在一起

### 容器

容器是用镜像创建的运行实例，Docker 利用容器独立运行一个或一组应用。它可以被启动、开始、停止、删除，每个容器都是相互隔离的、保证安全的平台。 可以把容器看作是一个简易的 Linux 环境和运行在其中的应用程序。容器的定义和镜像几乎一模一样，也是一堆层的统一视角，唯一区别在于容器的最上面那一层是可读可写的

容器的存在离不开镜像的支持，他是镜像运行时的一个载体（类似于实例和类的关系）。依托 Docker 的虚拟化技术，给容器创建了独立的端口、进程、文件等“空间”，Container 就是一个与宿机隔离 “容器”。容器可宿主机之间可以进行 port、volumes、network 等的通信。

镜像生成的容器本身也是一个文件，容器停止运行但是容器的文件还是存在。终止运行的容器依然会占据磁盘空间，可以使用 docker container rm \[containerId] 命令进行删除

### 仓库

仓库是集中存放镜像文件的场所。仓库和仓库注册服务器是有区别的，仓库注册服务器上往往存放着多个仓库，每个仓库中又包含了多个镜像，每个镜像有不同的标签。 仓库分为公开仓库和私有仓库两种形式，最大的公开仓库是 DockerHub，存放了数量庞大的镜像供用户下载，国内的公开仓库有阿里云、网易云

## 特点

__可移植性__：不依赖具体的操作系统或云平台，比如在阿里云或腾讯云直接随意迁移。

__占地小__：容器只需要其应用程序以及它需要运行的所有容器和库的依赖清单，不需要将所有的依赖库都打包在一起。

__共享 bin 和 lib__：不同的容器可以共享 bin 和 lib，进一步节省了空间。

## Docker 基于 LXC 虚拟容器技术

Docker 技术是基于 LXC（Linux container- Linux 容器）虚拟容器技术的。

LXC，其名称来自 Linux 软件容器（Linux Containers）的缩写，一种操作系统层虚拟化（Operating system–level virtualization）技术，为 Linux 内核容器功能的一个用户空间接口。__它将应用软件系统打包成一个软件容器（Container），内含应用软件本身的代码，以及所需要的操作系统核心和库__。通过统一的名字空间和共用 API 来分配不同软件容器的可用硬件资源，创造出应用程序的独立沙箱运行环境，使得 Linux 用户可以容易的创建和管理系统或应用容器。

LXC 技术主要是借助 Linux 内核中提供的 CGroup 功能和 namespace 来实现的，通过 LXC 可以为软件提供一个独立的操作系统运行环境。

### cgroup 和 namespace 介绍

* __namespace 是 Linux 内核用来隔离内核资源的方式。__ 通过 namespace 可以让一些进程只能看到与自己相关的一部分资源，而另外一些进程也只能看到与它们自己相关的资源，这两拨进程根本就感觉不到对方的存在。具体的实现方式是把一个或多个进程的相关资源指定在同一个 namespace 中。Linux namespaces 是对全局系统资源的一种封装隔离，使得处于不同 namespace 的进程拥有独立的全局系统资源，改变一个 namespace 中的系统资源只会影响当前 namespace 里的进程，对其他 namespace 中的进程没有影响。

  （以上关于 namespace 介绍内容来自<https://www.cnblogs.com/sparkdev/p/9365405.html> ，更多关于 namespace 的呢内容可以查看这篇文章 ）。

* __CGroup 是 Control Groups 的缩写，是 Linux 内核提供的一种可以限制、记录、隔离进程组 (process groups) 所使用的物力资源 (如 cpu memory i/o 等等) 的机制。__

  （以上关于 CGroup 介绍内容来自 <https://www.ibm.com/developerworks/cn/linux/1506_cgroup/index.html> ，更多关于 CGroup 的内容可以查看这篇文章 ）。

### cgroup 和 namespace 两者对比

两者都是将进程进行分组，但是两者的作用还是有本质区别。namespace 是为了隔离进程组之间的资源，而 cgroup 是为了对一组进程进行统一的资源监控和限制。

## 常用命令

| 命令                                                   | 描述                                                         |
| ------------------------------------------------------ | ------------------------------------------------------------ |
| docker version                                         | 查看 docker 版本                                             |
| docker images                                          | 查看所有以下载的镜像,等价于 docker image ls 命令             |
| docker contianer ls                                    | 查看所有的容器                                               |
| docker container kill \[containerId]                   | 杀死正在运行的容器                                           |
| docker container run --rm                              | 在容器停止运行后自动删除容器文件                             |
| docker container start                                 | 启动一个已经存在的容器                                       |
| docker container stop \[containerId]                   | 停止运行指定 containerId 的容器                              |
| docker container exec -it \[containerId] /bin/bash     | 进入一个正在运行的容器                                       |
| docker container logs                                  | 查看 docker 容器的输出，即容器里面 Shell 的标准输出          |
| docker ps                                              | 查看正在运行的容器                                           |
| docker prune                                           | 清理临时的、没有被使用的镜像文件 -a --all: 删除所有没有用的镜像文件，而不仅仅是临时文件 |
| docker search \[keywords]                              | 查找 keywords 相关的镜像                                     |
| docker pull \[keywords:version]                        | 拉取名为 keywords 的镜像到本地，并且可以指定版本号           |
| docker rmi \[imageId]                                  | 删除指定的镜像，等价于 docker image rm \[imageId]，删除镜像前需要查看有没有被容器引用，可以使用 docker ps 命令进行查看 |
| docer run -d                                           | 设置容器在后台运行，同时运行成功后在终端输出容器的 ID        |
| docker cp \[Hostfile] \[containerId:containerFilePath] | 将主机中的文件复制到容器中, 这两个参数可以互换位置，实现容器到主机的文件复制 |
| docker inspect \[containerId]                          | 返回指定容器的详细信息,如监听的端口，绑定的 IP 地址等        |
| docker top \[containerId]                              | 查看指定容器运行的进程                                       |
|                                                        |                                                              |

## 容器的启动

```shell
$ docker run [options] [imageName]
```

启动容器，如果本地没有要启动的镜像，会自动下载,每次运行都新建一个容器

该命令用于创建并启动容器

### 常用的参数

```text
// 将主机的 hostPort 端口映射到容器的 contianerPort 端口，主机对应端口监听到的内容会转发到容器设置的端口中
-p [hostPort:containerPort]

-i 即使没有和终端链接，也要开放标准输入流，通常和 -t 参数一起使用
-t 设置 tty(终端工具)，如 bash

-v [hostDir:containerDir] 将主机 hostDir 目录挂载到容器的 containerDir 上

--privileged 让容器内的用户在容器内能够获取 root 权限

--env 向容器进程传递环境变量
```

```shell
$ docker start [name or id]
```

只是单纯的启动。不会新建容器
