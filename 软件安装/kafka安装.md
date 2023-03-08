安装
```
wget  https://archive.apache.org/dist/kafka/2.0.0/kafka_2.11-2.0.0.tgz

tar -zxvf kafka_2.11-2.0.0.tgz

cd kafka_2.11-2.0.0

创建配置文件
mkdir etc
cp config/zookeeper.properties etc
cd etc
查看zookeeper的配置文件
vim config/server.properties
```





搭建有三个broke的伪分布式节点

```
cd kafka_2.11-2.0.0

mkdir etc

cp config/zookeeper.properties etc

cp config/server.properties etc/server-0.properties

cp config/server.properties etc/server-1.properties

cp config/server.properties etc/server-2.properties
```

启动三个kafka实例
```

vim server-0.properties

1.修改brokeid

2.去掉listeners前的注释，并修改端口号

3.修改log.dirs=/tmp/kafka-logs




```

进入bin目录下 启动zookeeper
```
cd bin

./zookeeper-server-start.sh ../etc/zookeeper.properties

```
启动完后，我们再启动kafka的三个实例
分别打开三个窗口，进入kafka的bin目录执行

```
./kafka-server-start.sh ../etc/server-0.properties
```

### Kafka启动报错处理
java未找到

原来是Kafka的默认`/usr/bin/java`路径与我们实际的`$JAVA_HOME/bin/java`路径不一致导致的
```
设置一个软连接就可以
查看我们的JAVA_HOME路径，下面会用到
echo $JAVA_HOME

建立软连接
sudo ln -s /usr/local/jdk1.8/ /usr/bin/java
```

bin目录下执行，用来查看常用的参数
```
./kafka-topics.sh
```
执行，创建主题test
```
./kafka-topics.sh --zookeeper localhost:2181 --create --topic test --partitions 3 --replication-factor 2

```

查看主题
```
./kafka-topics.sh --zookeeper localhost:2181 --describe --topic test

```

新建窗口，创建一个消费者
在bin目录下，查看参数
```
./kafka-console-consumer.sh
```

```
./kafka-console-consumer.sh --bootstrap-server localhost:9092,localhost:9093,localhost:9094 --topic test

```
创建完毕后，因为没人发消息，所以消费者读取不到消息
所以创建一个生产者，
```
./kafka-console-producer.sh --broker-list localhost:9092,localhost:9093,localhost:9094 --topic test
```
然后会进入一个箭头窗口，我们就可以进行消息的发送了