```
docker pull apache/rocketmq:4.9.4
docker pull pangliang/rocketmq-console-ng
```


```
docker run -d \
--restart=always \
--name rmqnamesrv \
-p 9876:9876 \
-v F:/dockerconfig/rocketmq/data/namesrv/logs:/root/logs \
-v F:/dockerconfig/rocketmq/data/namesrv/stores:/root/store \
-e "MAX_POSSIBLE_HEAP=100000000" \
apache/rocketmq:4.9.4 \
sh mqnamesrv


docker run -d  \
--restart=always \
--name rmqbroker \
--link rmqnamesrv:namesrv \
-p 10911:10911 \
-p 10909:10909 \
-v F:/dockerconfig/rocketmq/data/broker/logs:/root/logs \
-v F:/dockerconfig/rocketmq/data/broker/store:/root/store \
-v F:/dockerconfig/rocketmq/conf/broker.conf:/opt/rocketmq-4.9.4/conf/broker.conf \
-e "NAMESRV_ADDR=namesrv:9876" \
-e "MAX_POSSIBLE_HEAP=200000000" \
apache/rocketmq:4.9.4 \
sh mqbroker -c /opt/rocketmq-4.9.4/conf/broker.conf 


docker run -d \
--restart=always \
--name rmqadmin \
-e "JAVA_OPTS=-Drocketmq.namesrv.addr=192.168.52.136:9876 \
-Dcom.rocketmq.sendMessageWithVIPChannel=false" \
-p 9999:8080 \
pangliang/rocketmq-console-ng


docker run -d \
--restart=always \
--name rmqadmin2 \
-e "JAVA_OPTS=-Drocketmq.namesrv.addr=172.19.0.2:9876 \
-Dcom.rocketmq.sendMessageWithVIPChannel=false" \
-p 9992:8080 \
pangliang/rocketmq-console-ng

```

拉取镜像

启动 NameServ

启动 Broker

启动console

http://localhost:9999

conf
```
vi /docker/rocketmq/conf/broker.conf
# 所属集群名称，如果节点较多可以配置多个
brokerClusterName = DefaultCluster
#broker名称，master和slave使用相同的名称，表明他们的主从关系
brokerName = broker-a
#0表示Master，大于0表示不同的slave
brokerId = 0
#表示几点做消息删除动作，默认是凌晨4点
deleteWhen = 04
#在磁盘上保留消息的时长，单位是小时
fileReservedTime = 48
#有三个值：SYNC_MASTER，ASYNC_MASTER，SLAVE；同步和异步表示Master和Slave之间同步数据的机制；
brokerRole = ASYNC_MASTER
#刷盘策略，取值为：ASYNC_FLUSH，SYNC_FLUSH表示同步刷盘和异步刷盘；SYNC_FLUSH消息写入磁盘后才返回成功状态，ASYNC_FLUSH不需要；
flushDiskType = ASYNC_FLUSH
# 设置broker节点所在服务器的ip地址
brokerIP1 = 192.168.52.136
# 磁盘使用达到95%之后,生产者再写入消息会报错 CODE: 14 DESC: service not available now, maybe disk full
diskMaxUsedSpaceRatio=95

```