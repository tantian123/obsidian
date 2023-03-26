目录
介绍
java api

安装

docker pull zookeeper:3.5.8

docker run -d --name zookeeper -p 2181:2181 zookeeper:3.5.8




java调用zook的api

[Overview (Apache ZooKeeper - Server 3.5.5 API)](https://zookeeper.apache.org/doc/r3.5.5/api/index.html)


maven
```
<dependency>
            <groupId>org.apache.zookeeper</groupId>
            <artifactId>zookeeper</artifactId>
            <version>3.5.5</version>
        </dependency>

        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter-api</artifactId>
            <version>5.5.2</version>
        </dependency>
```

