#kafka/kraft #docker

# Kafka_kraft模式启动

- Kraft模式启动
	- 编辑配置文件
		- ```
		  cd ./kafka/config/kraft
		  cp server.properties server.properties.bak
		  
		  vi server.properties
		  
		  log.dirs=./kafka/kraft-combined-logs  # 实际使用绝对路径
		  advertised.listeners=PLAINTEXT://本机真实IP:9092  # 允许外部访问
		  ```
	- 生成UUID
		- ```
		  ./bin/kafka-storage.sh random-uuid  # 45FsNTJXSR-yQstzKxJpew
		  ./bin/kafka-storage.sh  format -t 45FsNTJXSR-yQstzKxJpew -c ./config/kraft/server.properties
		  ```
	- 启动
		- ```
		  ./bin/kafka-server-start.sh -daemon ./config/kraft/server.properties
		  ```
		  


## 单节点

docker-compose.yml

```yaml
version: "3.8"

services:
  kafka:
    image: bitnami/kafka:3.6.1
    container_name: kafka-kraft
    restart: unless-stopped
    ports:
      - "9092:9092"   # 对外 PLAINTEXT 端口
      - "9093:9093"   # 可选：对外 CONTROLLER 端口
    environment:
      # === KRaft 通用配置 ===
      - KAFKA_ENABLE_KRAFT=yes
      - KAFKA_CFG_PROCESS_ROLES=broker,controller   # 单节点同时充当 broker 和 controller
      - KAFKA_CFG_CONTROLLER_LISTENER_NAMES=CONTROLLER
      - KAFKA_CFG_LISTENERS=PLAINTEXT://:9092,CONTROLLER://:9093
      - KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP=CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT
      - KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://localhost:9092   # 宿主机访问用
      # - KAFKA_BROKER_ID=1
      - KAFKA_CFG_CONTROLLER_QUORUM_VOTERS=1@kafka:9093             # 集群投票节点
      - KAFKA_CFG_NODE_ID=1
      - ALLOW_PLAINTEXT_LISTENER=yes                                # 允许 PLAINTEXT
      # === 可选：持久化 & JVM ===
      - KAFKA_HEAP_OPTS=-Xmx1G -Xms1G
    volumes:
      - kafka_data:/bitnami/kafka
      # - ./kraft:/bitnami/kafka/config   # 如果想本地调试配置文件，可取消注释
    networks:
      - kafka_net

volumes:
  kafka_data:

networks:
  kafka_net:
    driver: bridge
```

测试收发消息（宿主机直接执行）：

```sh
# 创建 topic
docker exec -it kafka-kraft kafka-topics.sh \
  --create --topic demo \
  --bootstrap-server localhost:9092 \
  --partitions 1 --replication-factor 1

# 发送消息
docker exec -it kafka-kraft kafka-console-producer.sh \
  --topic demo --bootstrap-server localhost:9092

# 另开终端，消费消息
docker exec -it kafka-kraft kafka-console-consumer.sh \
  --topic demo --bootstrap-server localhost:9092 --from-beginning
```

如需三节点集群，只需新增两个服务（kafka-2、kafka-3），保持镜像版本一致，修改：  
KAFKA_BROKER_ID、KAFKA_CFG_NODE_ID 分别设为 2、3  
KAFKA_CFG_ADVERTISED_LISTENERS 指向各自主机名或 IP  
KAFKA_CFG_CONTROLLER_QUORUM_VOTERS=1@kafka-1:9093,2@kafka-2:9093,3@kafka-3:9093  


## 3节点集群（都在一个机器上）


介绍

- 每个节点独占一个容器（kafka-1、kafka-2、kafka-3）
- 每个节点都同时扮演 broker + controller
- 对外暴露 9092/9093/9094，分别映射到宿主机
- 内部网络 kafka_net 里，用 19093/19094/19095 作为 controller 端口
- 元数据会自动初始化一次（第一次启动时随机选一个节点跑 kafka-storage.sh random-uuid）

docker-compose.yml

```yaml
version: "3.8"

services:
  kafka-1:
    image: bitnami/kafka:3.6.1
    container_name: kafka-1
    restart: unless-stopped
    ports:
      - "9092:9092"
    environment:
      - KAFKA_ENABLE_KRAFT=yes
      - KAFKA_CFG_PROCESS_ROLES=broker,controller
      - KAFKA_CFG_NODE_ID=1
      - KAFKA_CFG_CONTROLLER_LISTENER_NAMES=CONTROLLER
      - KAFKA_CFG_LISTENERS=PLAINTEXT://0.0.0.0:9092,CONTROLLER://0.0.0.0:19093
      - KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://localhost:9092
      - KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP=CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT
      - KAFKA_CFG_CONTROLLER_QUORUM_VOTERS=1@kafka-1:19093,2@kafka-2:19094,3@kafka-3:19095
      - ALLOW_PLAINTEXT_LISTENER=yes
      # 让 bitnami 镜像自动格式化存储目录
      - KAFKA_KRAFT_CLUSTER_ID=abcdefghijklmnopqrstuv   # 占位符，真正值由脚本替换
    volumes:
      - kafka_1_data:/bitnami/kafka
    networks:
      - kafka_net

  kafka-2:
    image: bitnami/kafka:3.6.1
    container_name: kafka-2
    restart: unless-stopped
    ports:
      - "9093:9092"
    environment:
      - KAFKA_ENABLE_KRAFT=yes
      - KAFKA_CFG_PROCESS_ROLES=broker,controller
      - KAFKA_CFG_NODE_ID=2
      - KAFKA_CFG_CONTROLLER_LISTENER_NAMES=CONTROLLER
      - KAFKA_CFG_LISTENERS=PLAINTEXT://0.0.0.0:9092,CONTROLLER://0.0.0.0:19094
      - KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://localhost:9093
      - KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP=CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT
      - KAFKA_CFG_CONTROLLER_QUORUM_VOTERS=1@kafka-1:19093,2@kafka-2:19094,3@kafka-3:19095
      - ALLOW_PLAINTEXT_LISTENER=yes
      - KAFKA_KRAFT_CLUSTER_ID=abcdefghijklmnopqrstuv
    volumes:
      - kafka_2_data:/bitnami/kafka
    networks:
      - kafka_net

  kafka-3:
    image: bitnami/kafka:3.6.1
    container_name: kafka-3
    restart: unless-stopped
    ports:
      - "9094:9092"
    environment:
      - KAFKA_ENABLE_KRAFT=yes
      - KAFKA_CFG_PROCESS_ROLES=broker,controller
      - KAFKA_CFG_NODE_ID=3
      - KAFKA_CFG_CONTROLLER_LISTENER_NAMES=CONTROLLER
      - KAFKA_CFG_LISTENERS=PLAINTEXT://0.0.0.0:9092,CONTROLLER://0.0.0.0:19095
      - KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://localhost:9094
      - KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP=CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT
      - KAFKA_CFG_CONTROLLER_QUORUM_VOTERS=1@kafka-1:19093,2@kafka-2:19094,3@kafka-3:19095
      - ALLOW_PLAINTEXT_LISTENER=yes
      - KAFKA_KRAFT_CLUSTER_ID=abcdefghijklmnopqrstuv
    volumes:
      - kafka_3_data:/bitnami/kafka
    networks:
      - kafka_net

volumes:
  kafka_1_data:
  kafka_2_data:
  kafka_3_data:

networks:
  kafka_net:
    driver: bridge
```




生成集群ID

```sh
CLUSTER_ID=$(docker run --rm bitnami/kafka:3.6.1 kafka-storage.sh random-uuid)
echo $CLUSTER_ID   # 复制下来
```

创建 topic（三副本，验证集群工作）

```sh
docker exec -it kafka-1 kafka-topics.sh \
  --create --topic demo3 \
  --bootstrap-server localhost:9092,localhost:9093,localhost:9094 \
  --partitions 6 --replication-factor 3
```

查看 ISR

```sh
docker exec -it kafka-1 kafka-topics.sh \
  --describe --topic demo3 \
  --bootstrap-server localhost:9092
```

能看到 3 个 broker 都在 ISR 里，即集群成功。

## 3节点在不同的机器上

介绍

- 把 3 台物理机（或虚拟机）当成 3 个「独立主机」，每台只跑一个 Kafka 容器；
- 用真实的「主机名 + 端口」互相可达即可，其余逻辑与单机 compose 完全一致。
- 下面给出最简可复制粘贴的「逐机器部署清单」，假设：
- 主机 A 192.168.10.101
- 主机 B 192.168.10.102
- 主机 C 192.168.10.103

生成 cluster.id

```sh
docker pull bitnami/kafka:3.6.1
CLUSTER_ID=$(docker run --rm bitnami/kafka:3.6.1 kafka-storage.sh random-uuid)
echo $CLUSTER_ID          # 复制 22 位字符串
```

主机A docker-compose.yml

```yaml
version: "3.8"
services:
  kafka:
    image: bitnami/kafka:3.6.1
    restart: unless-stopped
    ports:
      - "9092:9092"
      - "19093:19093"
    environment:
      - KAFKA_ENABLE_KRAFT=yes
      - KAFKA_CFG_PROCESS_ROLES=broker,controller
      - KAFKA_CFG_NODE_ID=1
      - KAFKA_CFG_CONTROLLER_LISTENER_NAMES=CONTROLLER
      - KAFKA_CFG_LISTENERS=PLAINTEXT://0.0.0.0:9092,CONTROLLER://0.0.0.0:19093
      - KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://192.168.10.101:9092
      - KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP=CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT
      - KAFKA_CFG_CONTROLLER_QUORUM_VOTERS=1@192.168.10.101:19093,2@192.168.10.102:19093,3@192.168.10.103:19093
      - KAFKA_KRAFT_CLUSTER_ID=<上一步生成的 CLUSTER_ID>
      - ALLOW_PLAINTEXT_LISTENER=yes
    volumes:
      - kafka_data:/bitnami/kafka

volumes:
  kafka_data:
```

主机B docker-compose.yml

```
...
      - KAFKA_CFG_NODE_ID=2
      - KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://192.168.10.102:9092
...
```

主机C docker-compose.yml

```
...
      - KAFKA_CFG_NODE_ID=3
      - KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://192.168.10.103:9092
...
```

验证集群

- 任选一台，例如 A，执行：

```sh
# 创建三副本 topic
docker exec -it <kafka-container-id> \
  kafka-topics.sh --create --topic test3 \
  --bootstrap-server 192.168.10.101:9092,192.168.10.102:9092,192.168.10.103:9092 \
  --partitions 6 --replication-factor 3

# 描述 topic
docker exec -it <kafka-container-id> \
  kafka-topics.sh --describe --topic test3 \
  --bootstrap-server 192.168.10.101:9092
```

只要 Replicas 和 Isr 都列有 1,2,3，说明三台机器已正常组成 KRaft 集群。

生产环境请关掉 ALLOW_PLAINTEXT_LISTENER=yes，启用 SSL/SASL。

如需挂载宿主机目录，把 volumes 改成 - /data/kafka:/bitnami/kafka 即可。