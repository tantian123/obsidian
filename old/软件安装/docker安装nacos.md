```

docker pull nacos/nacos-server:v2.1.1


docker run --env PREFER_HOST_MODE=hostname --env MODE=standalone --env NACOS_AUTH_ENABLE=true -p 8848:8848 -p 9848:9848 -p 9849:9849 --name nacos nacos/nacos-server:v2.1.1

进入localhost:8848/nacos
初始账户密码均为 nacos

```

mysql下
[Release 2.1.1 (Aug 8th, 2022) · alibaba/nacos · GitHub](https://github.com/alibaba/nacos/releases/tag/2.1.1)
下载sql
```
docker stop nacosMysql && docker rm nacosMysql
docker stop myNacos && docker rm myNacos

docker run -d -p 3366:3306 --name nacosMysql -e MYSQL_ROOT_PASSWORD=password -e TZ=Asia/Shanghai mysql:latest 

docker exec -it nacosMysql mysql -uroot -p


docker run -d -e PREFER_HOST_MODE=hostname -e MODE=standalone -e SPRING_DATASOURCE_PLATFORM=mysql -e MYSQL_SERVICE_HOST=172.23.176.1 -e MYSQL_SERVICE_PORT=3366 -e MYSQL_SERVICE_USER=root -e MYSQL_SERVICE_PASSWORD=password -e MYSQL_SERVICE_DB_NAME=nacos -p 8848:8848 -p 9848:9848 -p 9849:9849 --name myNacos nacos/nacos-server:v2.1.1 --restart=always



```


mysql+集群
```
docker stop myNacos1 && docker rm myNacos1
docker stop myNacos2 && docker rm myNacos2
docker stop myNacos3 && docker rm myNacos3

docker run -d -e PREFER_HOST_MODE=hostname -e MODE=cluster -e NACOS_APPLICATION_PORT=8848 -e NACOS_SERVERS="192.168.1.131:8848 192.168.1.131:8858 192.168.1.131:8868" -e SPRING_DATASOURCE_PLATFORM=mysql -e MYSQL_SERVICE_HOST=172.23.176.1 -e MYSQL_SERVICE_PORT=3366 -e MYSQL_SERVICE_USER=root -e MYSQL_SERVICE_PASSWORD=password -e MYSQL_SERVICE_DB_NAME=nacos -e NACOS_SERVER_IP=192.168.1.131 -p 8848:8848 -p 9848:9848 -p 9849:9849 --name myNacos1 nacos/nacos-server:v2.1.1 --restart=always

docker run -d -e PREFER_HOST_MODE=hostname -e MODE=cluster -e NACOS_APPLICATION_PORT=8858 -e NACOS_SERVERS="192.168.1.131:8848 192.168.1.131:8858 192.168.1.131:8868" -e SPRING_DATASOURCE_PLATFORM=mysql -e MYSQL_SERVICE_HOST=172.23.176.1 -e MYSQL_SERVICE_PORT=3366 -e MYSQL_SERVICE_USER=root -e MYSQL_SERVICE_PASSWORD=password -e MYSQL_SERVICE_DB_NAME=nacos -e NACOS_SERVER_IP=192.168.1.131 -p 8858:8858 -p 9858:9858 -p 9859:9859 --name myNacos2 nacos/nacos-server:v2.1.1 --restart=always

docker run -d -e PREFER_HOST_MODE=hostname -e MODE=cluster -e NACOS_APPLICATION_PORT=8868 -e NACOS_SERVERS="192.168.1.131:8848 192.168.1.131:8858 192.168.1.131:8868" -e SPRING_DATASOURCE_PLATFORM=mysql -e MYSQL_SERVICE_HOST=172.23.176.1 -e MYSQL_SERVICE_PORT=3366 -e MYSQL_SERVICE_USER=root -e MYSQL_SERVICE_PASSWORD=password -e MYSQL_SERVICE_DB_NAME=nacos -e NACOS_SERVER_IP=192.168.1.131 -p 8868:8868 -p 9868:9868 -p 9869:9869 --name myNacos3 nacos/nacos-server:v2.1.1 --restart=always

```