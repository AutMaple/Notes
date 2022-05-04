# 使用 Docker 搭建 ZooKeeper 集群

## 下载镜像

```shell
$ docker pull zookeeper:3.7.0
```

## StandAlone 模式启动

### 启动容器

```shell
$ docker run -p 8080:8080 --name zookerper-standalone --restart always -d zookeeper:3.7.0
```

参数说明

- `-p` : 将主机 8080 端口映射到容器的 8080 端口
- `--name`: 设置容器的名字
- `--restart`:  由于 ZooKeeper 是 “快速失败”， 因此最好是通过 `--restart` 参数指定 ZooKeeper 意外停止之后自动重启
- `-d`: 设置容器在后台运行，同时运行成功之后会在终端输出容器的 ID

```shell
$ docker ps
```

输出

```text
2CONTAINER ID   IMAGE             COMMAND                  CREATED         STATUS         PORTS                                                                     NAMES
88bd956ca4fc   zookeeper:3.7.0   "/docker-entrypoint.…"   6 minutes ago   Up 6 minutes   2181/tcp, 2888/tcp, 3888/tcp, 0.0.0.0:8080->8080/tcp, :::8080->8080/tcp   zookerper-standalone
```

从上面的数据可以看出，容器暴露了 2181， 2888， 3888， 8080 端口

- 2181 端口：ZooKeeper 客户端端口
- 2888 端口：ZooKeeper 主从节点交互的端口
- 3888 端口：ZooKeeper 选举节点
- 8080 端口：ZooKeeper AdminServer 端口

暴露端口主要是镜像包含了 `EXPOSE 2181 2888 3888 8080` 命令

Zookeeper 3.5 版本开始提供 AdminServer 功能。AdminServer 是嵌入式 Jetty 服务器，为四字母命令提供 HTTP 接口。默认端口是 8080。由于在启动容器时做了端口映射，我们可以直接通过 `http://localhost:8080/commands/stats` 进行访问：

```json
{
  "version" : "3.7.0-e3704b390a6697bfdf4b0bef79e3da7a4f6bac4b, built on 2021-03-17 09:46 UTC",
  "read_only" : false,
  "server_stats" : {
    "packets_sent" : 0,
    "packets_received" : 0,
    "fsync_threshold_exceed_count" : 0,
    "client_response_stats" : {
      "last_buffer_size" : -1,
      "min_buffer_size" : -1,
      "max_buffer_size" : -1
    },
    "uptime" : 803381,
    "server_state" : "standalone",
    "data_dir_size" : 0,
    "log_dir_size" : 457,
    "last_processed_zxid" : 0,
    "outstanding_requests" : 0,
    "provider_null" : false,
    "avg_latency" : 0.0,
    "max_latency" : 0,
    "min_latency" : 0,
    "num_alive_client_connections" : 0,
    "auth_failed_count" : 0,
    "non_mtlsremote_conn_count" : 0,
    "non_mtlslocal_conn_count" : 0
  },
  "client_response" : {
    "last_buffer_size" : -1,
    "min_buffer_size" : -1,
    "max_buffer_size" : -1
  },
  "node_count" : 5,
  "connections" : [ ],
  "secure_connections" : [ ],
  "command" : "stats",
  "error" : null
}
```

> 在 Docker 中容器默认是无法与外部通信，需要将容器内的端口映射到本地宿主机的端口，这样用户通过访问宿主机指定端口时相当于访问容器内的端口。

### 进入容器

```shell
$ docker exec -it zookeeper-standalone /bin/bash
```

ZooKeeper 的默认配置文件是在根目录下 `/conf` 文件夹中:

```shell
root@88bd956ca4fc:/# cat /conf/zoo.cfg
dataDir=/data
dataLogDir=/datalog
tickTime=2000
initLimit=5
syncLimit=2
autopurge.snapRetainCount=3
autopurge.purgeInterval=0
maxClientCnxns=60
standaloneEnabled=true
admin.enableServer=true
server.1=localhost:2888:3888;2181
```

使用 `zkServer.sh status` 查看 ZooKeeper 的状态

```shell
root@88bd956ca4fc:/# zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /conf/zoo.cfg
Client port found: 2181. Client address: localhost. Client SSL: false.
Mode: standalone
```

### 连接 ZooKeeper 服务

```shell
root@88bd956ca4fc:/# zkCli.sh -server localhost:2181
```

我们可以不用先进入容器再连接 Zookeeper 服务，使用如下命令一步到位：

```shell
$ docker exec -it zookerper-standalone zkCli.sh -server localhost:2181
```

## 集群模式

这次搭建 3 个 ZooKeeper 集群

### 启动容器

为了避免一个一个的启动，我们使用 docker-compose 的方式来启动 Zookeeper 集群。首先创建一个名为 docker-compose.yml 的文件, 其内容如下:

```yaml
version: '3.1'

services:
  zoo1:
    image: zookeeper:3.7.0
    restart: always
    hostname: zoo1
    ports:
      - 2181:2181
    environment:
      ZOO_MY_ID: 1
      ZOO_SERVERS: server.1=0.0.0.0:2888:3888;2181 server.2=zoo2:2888:3888;2181 server.3=zoo3:2888:3888;2181

  zoo2:
    image: zookeeper:3.7.0
    restart: always
    hostname: zoo2
    ports:
      - 2182:2181
    environment:
      ZOO_MY_ID: 2
      ZOO_SERVERS: server.1=zoo1:2888:3888;2181 server.2=0.0.0.0:2888:3888;2181 server.3=zoo3:2888:3888;2181

  zoo3:
    image: zookeeper:3.7.0
    restart: always
    hostname: zoo3
    ports:
      - 2183:2181
    environment:
      ZOO_MY_ID: 3
      ZOO_SERVERS: server.1=zoo1:2888:3888;2181 server.2=zoo2:2888:3888;2181 server.3=0.0.0.0:2888:3888;2181
```

上述配置将以副本模式启动 Zookeeper 3.7.0，同时会告诉 Docker 运行三个 Zookeeper 容器：zoo1、zoo2、zoo3，并分别将本地的 2181, 2182, 2183 端口绑定到对应的容器的 2181 端口上。

ZOO_MY_ID 和 ZOO_SERVERS 是搭建 Zookeeper 集群需要设置的两个环境变量, 其中 ZOO_MY_ID 表示 Zookeeper 服务的 id, 它是 1-255 之间的整数, 必须在集群中唯一。ZOO_SERVERS 是 Zookeeper 集群的主机列表。

接着我们在 docker-compose.yml 当前目录下运行如下命令：

```shell
$ COMPOSE_PROJECT_NAME=zookeeper_cluster docker-compose up -d
```

> 或者指定配置文件名称 COMPOSE_PROJECT_NAME=zookeeper_cluster docker-compose -f docker-compose.yml up -d。 我们在 docker-compose up 前添加 COMPOSE_PROJECT_NAME=zookeeper_cluster 环境变量是为我们的 compose 工程起一个名字。 

看到如下信息表示容器启动成功：

```shell
wy:zookeeper wy$ COMPOSE_PROJECT_NAME=zookeeper_cluster docker-compose up -d
Creating network "zookeeper_cluster_default" with the default driver
Creating zookeeper_cluster_zoo1_1 ... done
Creating zookeeper_cluster_zoo3_1 ... done
Creating zookeeper_cluster_zoo2_1 ... done
```

通过如下命令查看运行中的 Zookeeper 集群容器：

```shell
docker ps
```
