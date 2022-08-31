

## 搭建 zk 集群
vi docker-compose-zk.yml: 
```
version: '3.1'

services:
  zoo1:
    image: zookeeper
    restart: always
    privileged: true
    hostname: zoo1
    ports:
      - 2181:2181
    volumes: # 挂载数据
      - /Users/apple/dockerdata/zookeeper_home/node1/data:/data
      - /Users/apple/dockerdata/zookeeper_home/node1/datalog:/datalog
      - /Users/apple/dockerdata/zookeeper_home/node1/logs:/logs
    environment:
      ZOO_MY_ID: 1
      ZOO_SERVERS: server.1=0.0.0.0:2888:3888;2181 server.2=zoo2:2888:3888;2181 server.3=zoo3:2888:3888;2181
    networks:
      default:
        ipv4_address: 172.19.0.11

  zoo2:
    image: zookeeper
    restart: always
    privileged: true
    hostname: zoo2
    ports:
      - 2182:2181
    volumes: # 挂载数据
      - /Users/apple/dockerdata/zookeeper_home/node2/data:/data
      - /Users/apple/dockerdata/zookeeper_home/node2/datalog:/datalog
      - /Users/apple/dockerdata/zookeeper_home/node2/logs:/logs
    environment:
      ZOO_MY_ID: 2
      ZOO_SERVERS: server.1=zoo1:2888:3888;2181 server.2=0.0.0.0:2888:3888;2181 server.3=zoo3:2888:3888;2181
    networks:
      default:
        ipv4_address: 172.19.0.12

  zoo3:
    image: zookeeper
    restart: always
    privileged: true
    hostname: zoo3
    ports:
      - 2183:2181
    volumes: # 挂载数据
      - /Users/apple/dockerdata/zookeeper_home/node3/data:/data
      - /Users/apple/dockerdata/zookeeper_home/node3/datalog:/datalog
      - /Users/apple/dockerdata/zookeeper_home/node3/logs:/logs
    environment:
      ZOO_MY_ID: 3
      ZOO_SERVERS: server.1=zoo1:2888:3888;2181 server.2=zoo2:2888:3888;2181 server.3=0.0.0.0:2888:3888;2181
    networks:
      default:
        ipv4_address: 172.19.0.13

networks: # 自定义网络
  default:
    external:
      name: zoonet
```


创建网络：
```
docker network create --driver bridge --subnet=172.19.0.0/16 --gateway=172.19.0.1 zoonet
```

创建挂载目录：
```
mkdir -p /Users/apple/dockerdata/zookeeper_home/node1
mkdir -p /Users/apple/dockerdata/zookeeper_home/node2
mkdir -p /Users/apple/dockerdata/zookeeper_home/node3
```
启动：
```
docker-compose -f docker-compose-zk.yml up -d
```
```
Creating zookeeper_zoo3_1 ... done
Creating zookeeper_zoo1_1 ... done
Creating zookeeper_zoo2_1 ... done
```
暂停：
```
docker-compose -f docker-compose.yml stop
```
```
Stopping zookeeper_zoo1_1 ... done
Stopping zookeeper_zoo3_1 ... done
Stopping zookeeper_zoo2_1 ... done
```
删除：
```
docker-compose -f docker-compose.yml rm
```
```
Going to remove zookeeper_zoo1_1, zookeeper_zoo3_1, zookeeper_zoo2_1
Are you sure? [yN] y
Removing zookeeper_zoo1_1 ... done
Removing zookeeper_zoo3_1 ... done
Removing zookeeper_zoo2_1 ... done
```

## 搭建 kafka 集群
vi docker-compose-kafka.yml: 
```
version: '3.4'
services:
  kafka1:
    image: wurstmeister/kafka
    restart: always
    hostname: kafka1
    container_name: kafka1
    privileged: true
    ports:
      - 9092:9092
    environment:
      KAFKA_ADVERTISED_HOST_NAME: kafka1
      KAFKA_LISTENERS: PLAINTEXT://kafka1:9092
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka1:9092
      KAFKA_ADVERTISED_PORT: 9092
      KAFKA_ZOOKEEPER_CONNECT: zoo1:2181,zoo2:2181,zoo3:2181
    volumes:
      - /Users/apple/dockerdata/kafka_home/kafka1/logs:/kafka
    networks:
      kafka:
        ipv4_address: 172.19.1.11
    extra_hosts:
      zoo1: 172.19.0.11
      zoo2: 172.19.0.12
      zoo3: 172.19.0.13
  kafka2:
    image: wurstmeister/kafka
    restart: always
    hostname: kafka2
    container_name: kafka2
    privileged: true
    ports:
      - 9093:9093
    environment:
      KAFKA_ADVERTISED_HOST_NAME: kafka2
      KAFKA_LISTENERS: PLAINTEXT://kafka2:9093
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka2:9093
      KAFKA_ADVERTISED_PORT: 9093
      KAFKA_ZOOKEEPER_CONNECT: zoo1:2181,zoo2:2181,zoo3:2181
    volumes:
      - /Users/apple/dockerdata/kafka_home/kafka2/logs:/kafka
    networks:
      kafka:
        ipv4_address: 172.19.1.12
    extra_hosts:
      zoo1: 172.19.0.11
      zoo2: 172.19.0.12
      zoo3: 172.19.0.13
  kafka3:
    image: wurstmeister/kafka
    restart: always
    hostname: kafka3
    container_name: kafka3
    privileged: true
    ports:
      - 9094:9094
    environment:
      KAFKA_ADVERTISED_HOST_NAME: kafka3
      KAFKA_LISTENERS: PLAINTEXT://kafka3:9094
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka3:9094
      KAFKA_ADVERTISED_PORT: 9094
      KAFKA_ZOOKEEPER_CONNECT: zoo1:2181,zoo2:2181,zoo3:2181
    volumes:
      - /Users/apple/dockerdata/kafka_home/kafka3/logs:/kafka
    networks:
      kafka:
        ipv4_address: 172.19.1.13
    extra_hosts:
      zoo1: 172.19.0.11
      zoo2: 172.19.0.12
      zoo3: 172.19.0.13
networks:
  kafka:
    external:
      name: kafka
```

创建网络：
```
docker network create --driver bridge --subnet=172.19.1.0/16 --gateway=172.19.1.1 kafka
```

创建挂载目录：
```
mkdir -p /Users/apple/dockerdata/kafka_home/kafka1/logs
mkdir -p /Users/apple/dockerdata/kafka_home/kafka2/logs
mkdir -p /Users/apple/dockerdata/kafka_home/kafka3/logs
```
启动：
```
docker-compose -f docker-compose-kafka.yml up -d
```
暂停：
```
docker-compose -f docker-compose-kafka.yml stop
```

删除：
```
docker-compose -f docker-compose-kafka.yml rm
```


