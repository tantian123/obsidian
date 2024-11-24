
```
wget --no-check-certificate --no-cookies https://downloads.apache.org/zookeeper/zookeeper-3.8.0/apache-zookeeper-3.8.0-bin.tar.gz

tar -zxvf apache-zookeeper-3.8.0-bin.tar.gz

```


```
cd apache-zookeeper-3.8.0-bin


修改配置文件
cd conf 

mv zoo_sample.cfg zoo.cfg
```


```
启动
cd .. 

sudo bin/zkServer.sh start
```


如果报错找不到JAVAHOME,进入`Zookeeper`的`bin`目录下，修改`zkEnv.sh`文件,加上一行

```
cd bin
sudo vim zkEnv.sh

JAVA_HOME=/usr/local/jdk1.8/
```


### 查看zookeeper的状态
 如果此时不在zookeeper的bin 目录下，先进入到该目录下
执行命令查看zookeeper状态
```
./zkServer.sh    status
```