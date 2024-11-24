建议看
[CentOS上使用Docker安装Kafka - 掘金 (juejin.cn)](https://juejin.cn/post/7224514397007757349)

```
docker network create app-tier --driver bridge


docker pull wurstmeister/zookeeper

docker pull wurstmeister/kafka

sudo docker run -d --restart=always --log-opt max-size=100m --log-opt max-file=2 --network app-tier --name zookeeper -p 2181:2181 -v /etc/localtime:/etc/localtime  wurstmeister/zookeeper


sudo docker run -d --restart=always --log-opt max-size=100m --log-opt max-file=2 --name kafka --network app-tier -p 9092:9092 -e KAFKA_BROKER_ID=0 -e KAFKA_ZOOKEEPER_CONNECT=0.0.0.0:2181 -e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://192.168.31.153:9092 -e KAFKA_LISTENERS=PLAINTEXT://0.0.0.0:9092 -v /etc/localtime:/etc/localtime wurstmeister/kafka

sudo docker exec -it kafka /bin/bash
```





















kafka的验证
```
docker ps 查看镜像的id   4b871abb625e

docker exec -it 4b871abb625e /bin/sh

进入该目录下
cd /opt/kafka/bin  

创建一个topic
./kafka-topics.sh --create --bootstrap-server localhost:9092 --topic test-kafka --partitions 3 --replication-factor 1

查看topic
./kafka-topics.sh --list --bootstrap-server localhost:9092 
./kafka-topics.sh --describe --topic test-kafka --bootstrap-server localhost:9092

生产消息
./kafka-console-producer.sh --topic test-kafka --bootstrap-server localhost:9092

消费消息：（重开一个窗口执行）
./kafka-console-consumer.sh --topic test-kafka --bootstrap-server localhost:9092 --from-beginning

```





=================================================

创建一个网络
```
docker network create app-tier --driver bridge
```

安装zookeeper  -p：设置映射端口（默认2181）  -d：后台启动
```
docker run -d --name zookeeper-server \
--network app-tier \
-e ALLOW_ANONYMOUS_LOGIN=yes \
bitnami/zookeeper:latest
```

安装Kafka
安装并运行Kafka，  

-name：容器名称  
-p：设置映射端口（默认9092 ）  
-d：后台启动  
ALLOW_PLAINTEXT_LISTENER任何人可以访问  
KAFKA_CFG_ZOOKEEPER_CONNECT链接的zookeeper  
KAFKA_CFG_ADVERTISED_LISTENERS当前主机IP或地址
```
docker run -d --name kafka-server \
--network app-tier \
-p 9092:9092 \
-e ALLOW_PLAINTEXT_LISTENER=yes \
-e KAFKA_CFG_ZOOKEEPER_CONNECT=zookeeper-server:2181 \
-e KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://192.168.0.101:9092 \
bitnami/kafka:latest
```

kafka-map图形化管理工具
图形化管理工具  
访问地址：[http://服务器IP:9001/](http://xn--ip-fr5c86lx7z:9001/)  
DEFAULT_USERNAME：默认账号admin  
DEFAULT_PASSWORD：默认密码admin
```
 docker run -d --name kafka-map \
--network app-tier \
-p 9001:8080 \
-v /opt/kafka-map/data:/usr/local/kafka-map/data \
-e DEFAULT_USERNAME=admin \
-e DEFAULT_PASSWORD=admin \
--restart always dushixiang/kafka-map:latest
```
